# Document Authoring Guide

How to write and prepare your documents so the assistant can find the right answer in them.

---

## Why this guide exists

When you upload a document, the assistant does **not** read it from start to finish like a person would. It splits the document into many short passages, and for every question you ask, it pulls back only the few passages that look most related to your question. The answer you see is built from those passages alone.

This means: **how you write and structure your document directly determines what the assistant can answer from it.** A page that makes perfect sense to a human reader can be useless to the assistant if it relies on context the assistant never sees.

This guide gives you concrete rules to write documents that work well.

---

## 1. Choose the right file format

**Best:** Markdown (`.md`), plain text (`.txt`), Word (`.docx`).
**Good:** Text-based PDF (one you exported from Word or a similar tool), HTML, CSV.
**Risky:** Scanned PDFs, photos of documents, screenshots — only work if your administrator has enabled OCR.
**Avoid:** Password-protected files, files with DRM, files larger than 15 MB.

If you have a long PDF, prefer exporting it to Word or Markdown first. PDFs hide the document's structure and often produce worse results than the same content in DOCX or MD.

---

## 2. One topic per file

Several focused files always beat one giant file.

- A 500-page handbook covering everything is harder to search than 20 short documents on individual topics.
- Split by topic, audience, or process — not by chapter number.
- Each file should be answerable on its own: someone reading only that file should be able to follow it.

---

## 3. Use real headings

Use the heading styles in your editor (Heading 1, Heading 2, …) or Markdown `#`, `##`, `###`. **Do not fake headings with bold text or larger fonts** — the assistant cannot tell the difference between a real heading and a bold sentence.

Good headings:
- "How to request travel reimbursement"
- "Maximum reimbursable amount per region"
- "When does parental leave start?"

Bad headings:
- "General information"
- "Notes"
- "Section 3"
- "More details"

**Tip:** Phrase the heading as the question your reader will ask. "How do I reset my password?" retrieves better than "Password procedures".

---

## 4. Write each section so it stands alone

Every section may be retrieved on its own, with no surrounding context. So:

- **Avoid backward references.** "As discussed above", "see the previous section", "the latter", "this approach" — none of these work. Repeat the actual subject instead.
- **Repeat the subject by name.** Use "the travel reimbursement policy" rather than "it" or "this" once you've started a new paragraph.
- **Define abbreviations and jargon every section, not just once.** Spell out "EBITDA (earnings before interest, taxes, depreciation, and amortization)" the first time it appears in *each* section that uses it.
- **State the section's scope at the top.** "This section covers reimbursement for domestic travel only." A reader landing in the middle of the document needs to know what they're reading.

---

## 5. Front-load the answer

Put the conclusion in the **first sentence** of every paragraph and section. Justification, examples, and edge cases come after.

- Good: "Travel reimbursement is paid within 14 days of the receipt being submitted. Receipts must be uploaded to the portal in PDF form, and …"
- Less good: "After receipts are submitted to the portal in PDF form, and reviewed by the manager, and processed by accounting, payment is issued within 14 days."

The first sentence is the most likely to survive intact when the document is split into passages.

---

## 6. Write short paragraphs and use lists

- Keep paragraphs to **3–6 sentences**. Long monolithic paragraphs get cut mid-thought.
- Use **bullet lists** for facts, rules, eligibility criteria, and exceptions.
- Use **numbered lists** for step-by-step procedures. Write "1.", "2.", "3." — not "first", "then", "finally".
- One idea per paragraph. If a paragraph contains two distinct rules, split it.

---

## 7. Use consistent terminology

Pick one term per concept and use it everywhere.

- If your document is about *customers*, do not switch to *clients*, *account holders*, or *users* in different sections.
- Glossary at the top of the document is fine, but every section should still use the chosen term.

Use synonyms **on purpose** when readers might search with different words. Mention "PTO", "vacation", and "annual leave" together in the relevant section if all three are common in your audience.

---

## 8. Tables and structured data

- Convert tables to **Markdown** or **CSV** before uploading where possible.
- **Precede every table with a one-sentence description** of what it contains. A table on its own often retrieves poorly; the description sentence retrieves well and pulls the table along with it.
- For wide or long tables, consider rewriting the rows as plain sentences:
  - Instead of a row `| EU | 200 EUR | hotel + meals |`
  - Write: "In the EU region, the maximum reimbursement is 200 EUR and covers hotel and meals."
- Never put critical information **only** inside a screenshot or image of a table.

---

## 9. Include examples

Examples retrieve very effectively, because user questions often look like the example phrasing.

- "Example: An employee traveling from Munich to Berlin can claim a hotel up to 150 EUR per night."
- "Example: If a contract is signed on March 15, the cooling-off period ends on March 29."

Add at least one worked example to every section that describes a rule, formula, or process.

---

## 10. What to remove before uploading

These add noise and **hurt** retrieval quality:

- Repeated headers and footers on every page
- Page numbers, "Page 1 of 25"
- Watermarks ("Confidential", "Draft")
- Long copyright and legal boilerplate footers
- Tables of contents and indexes that simply list section titles
- Marketing fluff ("In today's fast-paced world…")
- Long unstructured logs, code dumps, or raw data tables
- Empty pages and section dividers

---

## 11. File names matter

The file name appears in citations. A good name helps both the assistant and the human reader.

- Good: `travel-policy-v3-2026-04.pdf`, `onboarding-engineering-2026.md`
- Bad: `final.pdf`, `scan001.pdf`, `Document1.docx`

Include in the file name:
- What the document is about
- The version or date

---

## 12. Keep documents up to date

- When a document changes, **re-upload the new version** and remove the old one. Otherwise the assistant may cite both.
- Put a short changelog at the **bottom** of the document, not the top. Otherwise the changelog dominates the first passage and crowds out the actual content.
- State the effective date and the document owner in the first 100 words.

---

## Pre-upload checklist

- [ ] One topic per file; file under 15 MB
- [ ] Real heading styles (not bold text)
- [ ] Headings phrased as questions or specific topics
- [ ] First sentence of each section states the conclusion
- [ ] Sections self-contained — no "see above" references
- [ ] Subjects named (no orphan "it" / "this" / "the latter")
- [ ] Abbreviations defined in every section that uses them
- [ ] Short paragraphs (3–6 sentences); bullet and numbered lists used
- [ ] Tables in Markdown / CSV, each with a description sentence
- [ ] At least one worked example per rule or process
- [ ] Headers, footers, watermarks, and boilerplate stripped
- [ ] Consistent terminology across the document
- [ ] Filename describes content and version
- [ ] Effective date / version stated at the top
- [ ] Changelog (if any) at the bottom

---

## Why my answers are wrong — common authoring problems

| What you see | Likely cause | What to change |
|---|---|---|
| The assistant says it cannot find something that **is** in the document | The relevant section relies on backward references ("see above") or has a vague heading | Rewrite the section to stand alone; rename the heading to a specific question |
| The assistant cites the wrong section | Several sections share similar wording or use the same vague heading | Use distinct, specific headings; remove repeated boilerplate |
| The assistant cites an outdated answer | The old version of the document is still uploaded | Delete and re-upload |
| The assistant cannot find a specific number or ID | The number sits alone in a table cell with no surrounding text | Add a sentence around the number: "The maximum amount is **200 EUR** for…" |
| Answers based on scanned PDFs are nonsense | The PDF is image-based and OCR is not enabled, or the scan quality is too low | Re-export the document from the original source as text-based PDF / DOCX / MD; or ask your administrator to enable OCR |
| Long tables produce wrong answers | The table is too wide or has merged cells | Convert the table into sentences, one per row |
| Diagrams or screenshots contain key info that the assistant ignores | The assistant cannot read images by default in retrieval | Describe the diagram's content in adjacent text |
