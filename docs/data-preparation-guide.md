# Data Preparation Guide for LibreChat Knowledge Bases

A practical guide for customers ingesting documents into LibreChat agents (file search / RAG). Following these practices materially improves retrieval quality and answer accuracy.

## Pipeline at a glance
Upload → parse (Mistral OCR or native: pdfjs / mammoth / xlsx) → chunk + embed via RAG API → store in PGVector → retrieve top citations (defaults: `minRelevanceScore 0.45`, `maxCitations 30`, `maxCitationsPerFile 7`). Default embedding: `text-embedding-3-small`.

---

## Part A — Recommended configuration (admins)

Concrete starting values, not just available knobs. Adjust from these baselines.

### Embeddings (`.env`)
- `EMBEDDINGS_PROVIDER=openai`
- `EMBEDDINGS_MODEL=text-embedding-3-large` — upgrade from `-small` for any non-trivial deployment. ~3× cost, but a measurable recall improvement on long, technical, or multilingual content. Keep `-small` only for cost-sensitive English-only FAQ-style corpora.
- Re-embed the entire corpus when you change models — vectors from different models are not comparable.

### OCR (`librechat.yaml`)
Enable always, even if you "only have text PDFs" — there will be scans:
```yaml
ocr:
  apiKey: '${OCR_API_KEY}'
  baseURL: 'https://api.mistral.ai/v1'
  mistralModel: 'mistral-ocr-latest'
  strategy: mistral_ocr
```

### Retrieval thresholds (`librechat.yaml` → `endpoints.agents`)
| Use case | `minRelevanceScore` | `maxCitations` | `maxCitationsPerFile` |
|---|---|---|---|
| Customer support / FAQ (precision-first) | `0.55`–`0.65` | `10`–`15` | `3`–`5` |
| Internal knowledge base (balanced, default) | `0.45` | `20`–`30` | `5`–`7` |
| Research / discovery (recall-first) | `0.30`–`0.35` | `30`–`50` | `7`–`10` |

Lower threshold = more recall but more noise. Raise the threshold if the model frequently hallucinates from low-similarity chunks.

### File limits (`librechat.yaml` → `fileConfig`)
- `serverFileSizeLimit: 50` (MB) — keep under the 15 MB native parser cap *per file* unless you have validated the RAG API path for larger files.
- `endpoints.agents.fileLimit: 10` — a useful upper bound; more files dilute retrieval.
- Restrict `supportedMimeTypes` to formats you actually parse well (PDF, DOCX, MD, TXT, CSV, XLSX). Block image-only formats unless OCR is enabled.

### Operational
- Monitor RAG API health (`/health`); ingestion silently falls back to native parsing when it's down.
- Re-ingest after any chunking, OCR, or embedding-model change.
- Keep one PGVector instance per environment (dev/stage/prod) — schema changes in the RAG API can be incompatible.

---

## Part B — Recommendations for document authors

Customers often write the documents they ingest themselves. The single biggest accuracy lever is *how you write*, not how you tune the pipeline. Treat each document as a set of ~500-token chunks that will be retrieved out of order, in isolation.

### Write for chunk-level retrieval
- **Front-load every section.** First sentence states the conclusion or definition; the rest justifies. Chunks often cut after a few sentences.
- **Make every paragraph self-contained.** Avoid "as discussed above", "see Section 3", "the latter", "this approach". A retrieved chunk has no "above".
- **Repeat the subject by name.** Use "the travel reimbursement policy" instead of "it" / "this" / "the policy" after the first mention. Embeddings match on the words present.
- **Define jargon inline on first use within each section** — not just once at the start of the document. "EBITDA (earnings before interest, taxes, depreciation, and amortization)" should appear in every section that uses the term.
- **Use synonyms / alternative phrasings deliberately.** If users will ask "PTO", "vacation days", "annual leave" — include all three terms in the relevant section. Embeddings match semantics, but a literal token overlap still helps, especially for IDs and acronyms.

### Structure
- **Use real heading styles**, not bold + larger font. The parser uses heading metadata for chunk boundaries.
- **One concept per heading.** Headings like "General Information" or "Notes" are useless for retrieval. Prefer "How to request travel reimbursement" or "Maximum reimbursable amounts per region".
- **Phrase headings as the question users will ask.** "Wie beantrage ich Urlaub?" retrieves better than "Antragsverfahren".
- **Short paragraphs (3–6 sentences).** Long monolithic paragraphs get chopped mid-thought.
- **Use bullet lists for facts, rules, and steps.** They survive chunking well and embed cleanly.
- **Number procedural steps explicitly** ("1.", "2."), not "first", "then", "finally" — numbers are stable retrieval anchors.

### Content quality
- **State the document's scope, audience, and effective date in the first 100 words.** This anchors every chunk's context.
- **Include worked examples.** "Example: An employee traveling from Munich to Berlin can claim …" — examples retrieve very effectively because user questions often look like the example phrasing.
- **Don't hide information in diagrams or screenshots only.** Always describe the diagram's content in adjacent text.
- **Avoid "see Appendix A" cross-references for important content.** Inline it. The appendix and the question rarely retrieve together.
- **Use consistent terminology.** Pick one term per concept and stick to it across the document set. "Customer", "client", "account holder" used interchangeably costs you retrieval precision.
- **Spell out numbers and units.** "30 days" not "30 d"; "EUR 500" not "500".

### Content to remove
- Repeated headers, footers, page numbers, legal boilerplate, watermarks, "Confidential" stamps on every page.
- Copy-pasted tables of contents and indexes (they create noisy near-duplicate chunks).
- Marketing fluff and filler ("In today's fast-paced world…").
- Long unstructured logs, code dumps, or raw data tables — link or attach instead.

### Tables and structured data
- **Convert tables to Markdown or CSV** before ingestion when possible.
- **Precede every table with a one-sentence description** of what it contains. The description chunks well; the table rows alone often don't.
- **Repeat column headers in each row** for very wide tables ("Region: EU, Max amount: 200, …") — denormalized text retrieves more reliably than tabular structure.
- For long tables, consider exporting key rows as natural-language sentences ("In the EU region, the maximum reimbursement is 200 EUR.").

### File hygiene
- One topic per file. Multiple short, focused files beat one giant file.
- Filename = content + version + date: `travel-policy-v3-2026-04.md`.
- No password protection or DRM.
- Re-export scans through OCR before ingesting (or rely on Mistral OCR if configured).
- Keep a changelog / version history at the bottom of each document, not the top — so it doesn't dominate the first chunk.

---

## Pre-ingestion checklist
- [ ] Text-native format (or OCR enabled for scans)
- [ ] Under 15 MB per file; password-free
- [ ] Real heading styles; one concept per heading
- [ ] Short paragraphs; first sentence states the point
- [ ] Sections self-contained; subjects named, not pronouns
- [ ] Acronyms defined inline per section
- [ ] Tables in Markdown/CSV with a description sentence
- [ ] Boilerplate, headers, footers, watermarks stripped
- [ ] Worked examples included
- [ ] Filename describes content and version
- [ ] One topic per file

## Common failure modes
| Symptom | Likely cause | Fix |
|---|---|---|
| Citations empty or nonsensical | Scanned PDF, no OCR configured | Enable Mistral OCR, or pre-OCR the file |
| "Cannot find" content that is in the doc | Layout broke chunks (multi-column, tables) | Re-export as DOCX or Markdown |
| Wrong section cited | Vague headings, repeated phrases | Use specific question-shaped headings; remove boilerplate |
| Wrong file cited | Generic filenames; overlapping topics | Rename descriptively; split topics across agents |
| Numeric / ID lookups miss | Pure vector search weak on tokens | Surround the ID with descriptive text in the same sentence |
| Answers cite outdated info | Old versions still embedded | Delete and re-ingest after every doc revision |
| Large-file ingest fails / hangs | Over 15 MB or 5-minute parse timeout | Split the file; check RAG API health |
| Model paraphrases incorrectly | Threshold too low, low-similarity chunks pulled in | Raise `minRelevanceScore` to `0.55`+ |
