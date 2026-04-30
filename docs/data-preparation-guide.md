# Data Preparation Guide for LibreChat Knowledge Bases

A one-pager for customers ingesting documents into LibreChat agents (file search / RAG). Following these practices materially improves retrieval quality and answer accuracy.

## Pipeline at a glance
Upload → parse (Mistral OCR or native: pdfjs / mammoth / xlsx) → chunk + embed via RAG API → store in PGVector → retrieve top citations (defaults: `minRelevanceScore 0.45`, `maxCitations 30`, `maxCitationsPerFile 7`). Default embedding: `text-embedding-3-small`.

## File preparation
- **Prefer text-native formats.** Markdown, plain text, HTML, and DOCX yield the cleanest parses. Re-export scanned PDFs as text-native PDFs whenever possible.
- **Enable OCR for scanned content.** Configure `ocr` in `librechat.yaml` with a Mistral key — without it, scanned/image-only PDFs ingest as empty text and silently produce useless embeddings.
- **Stay under the parser limits.** Native parser cap is **15 MB** per file (50 MB for ODT). Split or pre-process larger files; very large files can also hit the 5-minute RAG parse timeout.
- **Avoid encrypted/password-protected PDFs.** They are rejected (notably for Anthropic endpoints).
- **Strip non-content noise.** Remove headers, footers, page numbers, legal boilerplate, watermarks, and repeated tables of contents — they dilute embeddings and crowd out signal.
- **Flatten complex layouts.** Multi-column PDFs, dense tables, and sidebars confuse the native PDF parser. Re-export to Markdown / DOCX, or route through OCR.
- **Tables → CSV or Markdown.** A 50-row Markdown table embeds far better than a screenshot or image-based table.
- **One topic per file when possible.** Several focused files retrieve more cleanly than one 500-page omnibus PDF.
- **Use descriptive, versioned filenames.** Filenames appear in citations and provide retrieval signal — e.g. `policy-travel-2026-04.pdf` beats `scan001.pdf`.

## Document structure
- **Add headings.** Use clear `#`/`##` Markdown or DOCX heading styles. Heading-aware chunking benefits significantly from explicit boundaries.
- **Self-contained sections.** Each section should make sense alone — avoid "as discussed above" references that lose meaning when a chunk is retrieved in isolation.
- **Spell out acronyms once per section.** Embeddings won't infer "EBITDA" from upstream context the way a human reader does.
- **Front-load the answer.** Put key facts in the first sentence of each paragraph; chunks often cut mid-section.
- **Avoid huge code/log dumps inline.** Trim to relevant excerpts or move to attachments.

## Organization & metadata
- Encode document type, date, and version in the filename.
- Group related files into a single agent's knowledge base; isolate unrelated topics across separate agents to reduce cross-talk.
- Maintain a manifest (spreadsheet or README) of what's ingested — useful for audit and re-ingestion after schema or chunking changes.

## Tuning retrieval (admin)
In `librechat.yaml` under `endpoints.agents`:
- `minRelevanceScore` (default `0.45`) — raise to `0.55`–`0.65` for stricter, higher-precision answers; lower to `0.30` when recall matters more than precision.
- `maxCitations` (default `30`) and `maxCitationsPerFile` (default `7`) — cap to fit your model's context budget.
- `EMBEDDINGS_MODEL` (env) — upgrade to `text-embedding-3-large` for better recall on long-form, technical, or multilingual content (~3× cost).

## Pre-ingestion checklist
- [ ] Text-native format (or OCR enabled for scans)
- [ ] Under 15 MB per file
- [ ] No password protection
- [ ] Headings present; sections self-contained
- [ ] Tables in Markdown / CSV form
- [ ] Headers, footers, and boilerplate stripped
- [ ] Filename describes content and version
- [ ] One topic per file

## Common failure modes
| Symptom | Likely cause | Fix |
|---|---|---|
| Citations empty or nonsensical | Scanned PDF, no OCR configured | Enable Mistral OCR, or pre-OCR the file |
| "Cannot find" content that is in the doc | Layout broke chunks (multi-column, tables) | Re-export as DOCX or Markdown |
| Wrong section cited | Vague headings, repeated phrases | Use specific headings; remove repeated boilerplate |
| Large-file ingest fails / hangs | Over 15 MB or 5-minute parse timeout | Split the file; verify RAG API health |
| Wrong file cited | Generic filenames | Rename with content + version |
| Numeric / ID lookups miss | Pure vector search weak on tokens | Include the ID in surrounding sentence text |
