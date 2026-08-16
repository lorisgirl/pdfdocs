# PDF.co merge documentation style guide

Rules for the PDF.co merge sample under `pdfco-merge/`. AI skills and editorial review enforce these constraints.

## Product and endpoint naming

- **PDF.co** — official product name. Not "Pdf.co" or "PDFCo".
- **Merge PDF API** — reader-facing name for `POST /v1/pdf/merge`.
- **`POST /v1/pdf/merge2`** — separate multi-format merge endpoint. Do not describe it as a mode of the merge endpoint unless that page is explicitly scoped to merge2.
- **Job Check** — prose name for the Background Job Check endpoint. Use **Job Check** or link text **Background Job Check**. Do not use `job-check` in prose (URLs may keep the path `/api/job-check`).

## Voice and tone

- Second person, present tense: "Send a POST request", not "The user sends".
- Active voice: "PDF.co returns a `jobId`", not "A `jobId` is returned".
- Direct and concise. Readers often scan under time pressure.
- No marketing language: avoid "powerful", "seamless", "robust", "cutting-edge".

## Terminology glossary

| Use | Not |
|---|---|
| merge request | merge call, combine operation |
| attribute (request/response JSON fields) | parameter (except query strings, which this endpoint does not use) |
| Job Check | job-check, job check endpoint (in prose) |
| synchronous / asynchronous | sync/async in running prose (backticks OK in tables) |
| output URL / `url` field | download link, file link (inconsistent alternates) |
| credits consumed (`credits` field) | points, tokens |
| API Tester | sandbox, playground (unless quoting PDF.co UI label) |
| Prerequisites (section heading) | Before you begin, Before you start, pre-requisites |

Define merge-specific terms on first use in the tutorial. Use glossary terms consistently on other pages.

## Punctuation and lists

- Prefer single sentences over compound sentences joined with semicolons.
- Do not use semicolons to separate list items. End each item with a period.
- Capitalize the first word of each list item.
- In lists, use **Label:** Text instead of em dashes. Example: `- **Risk level:** High.`

## Headings

- H1: page title only. One H1 per page.
- H2: major sections. H3: subsections.
- Do not skip levels (no H1 → H3).
- Sentence case: "Merge PDF files", not "Merge PDF Files".

## Code examples

- Use `bash` for cURL commands.
- Use `json` for request and response bodies.
- Placeholder API key: `YOUR_API_KEY`. Placeholder URLs: `https://example.com/...`.
- Never use real API keys, secrets, or customer production URLs.

## Formatting

- Backticks for code identifiers: paths, header names, field names.
- Tables for reference content (attributes, response fields, limits).
- Numbered lists for sequential steps in the tutorial.
- Bulleted lists for non-sequential items.

## Page-type conventions

| Type | Opening | Structure | Closing |
|---|---|---|---|
| Tutorial | State what the reader will accomplish | Prerequisites → walkthrough → interpret response | Related pages |
| Reference | One-line description | Tables + JSON samples | Related pages |
| Explanation | State what the reader will understand | Concept sections by topic | Related pages |
| Troubleshooting | State the diagnostic scope | Symptom → cause → fix | Related pages or escalation |

## Cross-linking

- Use relative Markdown links: `[Merge PDF API reference](./merge-reference.md)`.
- Link to the official [Merge PDF API reference](https://developer.pdf.co/api/merge/pdf) for behavior not duplicated locally.
- Link to troubleshooting from pages that mention async handoffs or common failures.
- Link to credits from pages that mention `credits` or `remainingCredits`.

## Links and URLs

Apply these rules when adding or reviewing links. Do not publish plausible-but-invented URLs.

### Link text

- Use descriptive link text that names the destination or action.
- Do not use "click here", "here", "this link", or a raw URL as the only link text.
- Good: `[Background Job Check endpoint](https://developer.pdf.co/api/job-check)`.
- Avoid: `[click here](https://developer.pdf.co/api/job-check)`.

### Relative links

- Every relative Markdown link must target a file that exists in the repository.
- Example: `[Merge PDF API reference](./merge-reference.md)`.

### External links

- Use HTTPS for external links.
- Placeholder example URLs must use an allowed domain (for example `https://example.com/...`).
- Never invent PDF.co paths, S3 buckets, or API routes that look realistic but are not confirmed.

### Known official PDF.co destinations

Use these URLs for this sample. If a link is not on this list, verify it against the live site before publishing.

- `https://developer.pdf.co/api/merge/pdf`
- `https://developer.pdf.co/api/job-check`
- `https://developer.pdf.co/api-tester/merge/pdf`
- `https://developer.pdf.co/api/response-codes`
- `https://developer.pdf.co/api/profiles`
- `https://developer.pdf.co/api`
- `https://app.pdf.co/subscriptions`
- `https://developer.pdf.co/api/file-upload/overview`
- `https://developer.pdf.co/knowledgebase/user-controlled-encryption`

### Media references

- Every image or media path must point to an existing asset, or the writer must add the asset before merge.
- Run an HTTP link check (for example `lychee` or `markdown-link-check`) on external URLs in CI or before merge when possible.

## Accessibility

Apply these rules in addition to [Headings](#headings), [Links and URLs](#links-and-urls), and [Formatting](#formatting). Do not change API facts to satisfy accessibility rules.

### No sensory-only language

- Do not rely on sight, sound, or spatial cues alone.
- Avoid spatial words such as "below", "above", "following", and "preceding" when they ask the reader to locate content by position on the page.
- Avoid: "see below", "the example below", "look at the screenshot", "the button on the right", "as shown in green", "the values above".
- Prefer section names, field names, or link text that names the destination: "In the **Response fields** section", "In [Response codes](url)", "Read [Merge PDF files](./merge-pdfs.md)".
- In tables, do not write "See [topic]" or "See table below". Write "Documented in [topic]" or name the section ("Profile tables in this page").

### Alt text on images

- Informative images need alt text that describes what the image conveys.
- Do not use generic alt such as "image", "screenshot", or "diagram".
- Decorative images in Markdown use empty alt: `![](path)`.
- Decorative images in HTML or MDX use `alt=""` and `aria-hidden="true"` (or `role="presentation"`).

### Heading hierarchy

- Follow the [Headings](#headings) rules: one H1 per page, no skipped levels, sentence case.
- Do not use headings only for visual size.

### Scannable tables

- Data tables must include a header row.
- Do not use tables for layout. Use prose or lists instead.

## Source of truth

- [PDF.co Merge PDF API reference](https://developer.pdf.co/api/merge/pdf) for fields, defaults, and limits.
- [API Tester](https://developer.pdf.co/api-tester/merge/pdf) for executable validation.
- [Credit-pricing page](https://app.pdf.co/subscriptions) for current rates.

Do not invent API behavior. If sources disagree, record the conflict and resolve with a human before publishing.

## Content boundaries

- Document `POST /v1/pdf/merge` unless the topic brief expands scope.
- Do not speculate about roadmap features.
- Label credit examples as illustrative when rates can change.
