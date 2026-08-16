---
name: editorial-review
description: Polishes PDF.co merge documentation against the style guide, verifies URLs and media are not hallucinated, and checks basic accessibility. Use for final copy-edit passes after SME approval and before merge.
disable-model-invocation: true
---

# Editorial review

Final prose polish pass. Enforces the style guide. Verifies links, media references, and basic accessibility. Does not change API facts.

## Instructions

You are an editorial reviewer for PDF.co merge documentation. Read and apply [pdfco-style-guide.md](../../rules/pdfco-style-guide.md) in full.

Do not change API endpoints, field names, code examples, or behavioral claims. Only adjust prose, headings, tone, and formatting.

Apply all style guide sections in order.


## Input the writer provides

```
{paste draft Markdown here}
```

## Tasks

### 1. Verify URLs and media

Inventory every link and media reference in the draft. Apply [Links and URLs](../../rules/pdfco-style-guide.md#links-and-urls).

- Classify each URL (relative, official PDF.co, placeholder, media).
- Flag hallucinated, unverified, or broken references.
- Mark unconfirmed PDF.co URLs with `[VERIFY]` in the findings list only.

### 2. Check accessibility

Apply [Accessibility](../../rules/pdfco-style-guide.md#accessibility).

- Report sensory-only language, including "below", "above", "following", and "see [link]" in tables. Suggest section names or "read [Page title]" instead.
- Use `[DECORATIVE]` in the findings list when an image is correctly treated as decorative. Do not put review markers in the source file.

### 3. Style and copy review

Apply the remaining style guide sections. For each issue found, provide:

1. Location (heading or line reference)
2. Issue type (link | accessibility | terminology | tone | structure | formatting | punctuation)
3. Current text
4. Suggested fix
5. Style guide section violated

Then provide a **unified diff** against the input file so the writer can review and approve each change. Do not replace the full page inline.

Use standard unified diff format with file paths and line context:

```diff
--- a/pdfco-merge/merge-pdfs.md
+++ b/pdfco-merge/merge-pdfs.md
@@ -12,7 +12,7 @@
-Old line the writer can reject.
+Suggested line the writer can accept.
```

Rules for the diff:

- One diff hunk per logical change when possible.
- Include enough surrounding lines for the writer to locate each edit.
- Omit hunks the writer should skip. Do not bundle unrelated changes in one hunk.
- If a finding has no safe automatic fix, list it in the annotated issue list only. Do not guess in the diff.
- Mark uncertain suggestions with `[REVIEW]` in the issue list, not inside the diff text.

The writer applies accepted hunks to the source file. Unaccepted hunks stay out of the published page.

## Expected output

1. A link and media inventory with verification status for each reference.
2. An accessibility findings list (or "no issues" note).
3. An annotated issue list with specific fixes (links, then accessibility, then style).
4. A unified diff against the input file for the writer to review and apply selectively.
5. `[REVIEW]` and `[VERIFY]` markers in the issue list only, not in the source file or diff body.

## Human review checklist

See [Links and URLs](../../rules/pdfco-style-guide.md#links-and-urls) and [Accessibility](../../rules/pdfco-style-guide.md#accessibility), plus:

- [ ] No API facts were changed (endpoints, fields, async behavior, credits)
- [ ] Terminology matches the style guide glossary (Job Check, merge endpoint)
- [ ] List items use periods, not semicolons
- [ ] Writer reviewed each diff hunk and applied only accepted changes
- [ ] `[REVIEW]` and `[VERIFY]` items are evaluated manually before applying related hunks
- [ ] Code blocks have language tags

## What this skill does not do

- Does not verify undocumented API behavior (SME fact-check gate).
- Does not restructure page types (use the structure-and-diataxis skill).
- Does not add or remove content sections (only polishes what exists).
