# How to prepare your documents — One-Pager

How to write and structure documents so the assistant can find the right answer in them.

## What the assistant supports

You can upload **PDF (text or scanned), Word (DOCX), Excel (XLSX, XLS, ODS), OpenDocument (ODT), Markdown, plain text, HTML, CSV, JSON, and common image formats**. Scanned PDFs and images are read by OCR when your administrator has enabled it. Avoid password-protected files. Maximum size: 15 MB per file.

## How the assistant reads your document

The assistant **does not read your document end to end**. It splits it into short passages and, for every question, only sees the few passages that look most related to that question. Every passage must therefore make sense on its own — that is the single most important idea in this guide.

## File rules

- **One topic per file.** Several focused files always beat one giant file.
- **Prefer text-native formats** when you have a choice: Word, Markdown, text-based PDF (one you exported from Word), Excel for tabular data.
- **Filename = topic + version + date** — e.g. `travel-policy-v3-2026-04.pdf`. Generic names like `final.pdf` or `scan001.pdf` hurt retrieval and confuse citations.
- Stay under **15 MB**; no password protection.

## Structure rules

- **Use real heading styles** (Heading 1 / 2 / 3 in Word, `#` `##` `###` in Markdown). Do **not** fake headings with bold text or larger fonts — the assistant cannot tell the difference.
- **Phrase headings as the question your reader will ask.** "How do I request travel reimbursement?" works far better than "Reimbursement procedures" or "Section 3".
- **Short paragraphs (3–6 sentences).** Use bullet lists for facts and numbered lists for step-by-step procedures.
- **One concept per heading.** No "General information", "Notes", "More details".

## Writing rules

- **Front-load the answer.** First sentence of every section states the conclusion; justification and edge cases come after.
- **Make every section stand alone.** No "as discussed above", "see the previous section", "the latter". A retrieved passage has no "above".
- **Repeat the subject by name.** Use "the travel reimbursement policy" instead of "it" or "this" once you start a new paragraph.
- **Define abbreviations in every section that uses them**, not only once at the top. "EBITDA (earnings before interest, taxes, depreciation, and amortization)".
- **One term per concept.** Do not switch between *customer*, *client*, *account holder*. Pick one and use it everywhere.
- **Add a worked example to every rule or process.** Example phrasing matches the way users ask questions, so examples are retrieved very effectively.

## Tables and structured data

- Convert tables to **Markdown** or **CSV** where possible.
- **Precede every table with a one-sentence description** of what it contains. The description sentence retrieves well and pulls the table along.
- For wide or long tables, also write the key rows as plain sentences — "In the EU region, the maximum reimbursement is 200 EUR." — so the data survives even if the table is split.
- Never put critical information **only** inside a screenshot or image of a table.

## Remove before uploading

Repeated headers and footers, page numbers, watermarks ("Confidential", "Draft"), legal boilerplate on every page, marketing fluff, copy-pasted tables of contents, empty section dividers.

## Pre-upload checklist

- [ ] One topic per file, under 15 MB, no password
- [ ] Real heading styles (not bold)
- [ ] Headings phrased as questions
- [ ] First sentence of each section states the conclusion
- [ ] Sections stand alone — no "see above" references
- [ ] Subjects named, not "it" / "this" / "the latter"
- [ ] Abbreviations defined per section
- [ ] Short paragraphs; bullet and numbered lists
- [ ] Tables in Markdown / CSV with description sentence
- [ ] At least one worked example per rule
- [ ] Headers, footers, watermarks, boilerplate removed
- [ ] Consistent terminology
- [ ] Filename describes content + version
- [ ] Effective date / version stated at the top; changelog (if any) at the bottom

## Common problems and fixes

| What you see | Likely cause | Fix |
|---|---|---|
| Cannot find content that **is** in the document | Section relies on backward references or has a vague heading | Rewrite section to stand alone; rename heading as a specific question |
| Wrong section cited | Multiple sections share similar wording or vague headings | Use distinct, specific headings; remove repeated boilerplate |
| Outdated answer | Old version of the document is still uploaded | Delete and re-upload |
| Number / ID not found | Number sits alone in a table cell with no surrounding text | Surround with descriptive text in the same sentence |
| Scanned PDF returns nonsense | Image-based PDF and OCR not enabled, or scan quality too low | Re-export as text-based PDF / DOCX / Markdown, or ask your admin to enable OCR |
| Wide / long tables produce wrong answers | Merged cells or very wide tables | Convert each row into a sentence |
| Diagrams or screenshots ignored | Images are not searched as text by default | Describe the diagram's content in adjacent text |
