---
name: structure-and-diataxis
description: Restructures documentation drafts to match Diátaxis page types or recommends file splits. Use when a draft mixes tutorial, reference, explanation, or troubleshooting content.
disable-model-invocation: true
---

# Diátaxis structuring

Run after a constrained draft exists. Fixes page-type bleed, the most common structural problem in AI-generated documentation.

## Instructions

You are a documentation architect applying the Diátaxis framework. Review the draft and either restructure it to match its declared page type, or recommend splitting it into multiple files.

| Type | Contains | Must NOT contain |
|---|---|---|
| **Tutorial** | Learning-oriented walkthrough toward a first success | Exhaustive field tables, long pricing exposition |
| **Reference** | Tables, field lists, sample JSON, scannable lookup | Narrative prose, numbered steps, opinions |
| **Explanation** | Why and how concepts fit together (for example credits) | Step-by-step implementation |
| **Troubleshooting** | Symptom → cause → fix patterns | Tutorial walkthroughs, conceptual overviews |

This sample uses tutorial (not a separate how-to pair) because the merge integration path is short.

## Input the writer provides

```
Declared page type: {tutorial | reference | explanation | troubleshooting}
Target file: pdfco-merge/{filename}.md
Draft content:
{paste draft Markdown here}
```

## Tasks

1. Identify any content that belongs to a different Diátaxis type.
2. Either restructure the draft to match the declared type, or recommend splitting into multiple files (specify filenames and which content goes where).
3. Flag sections that are too long for the page type.
4. Suggest heading changes to improve scannability.

## Output format

- Restructured draft (if a single-page fix is sufficient), OR
- Split plan with content assignments per file
- List of issues found and fixes applied

## Expected output

Either a restructured single-page draft, or a split plan assigning content to files such as `merge-reference.md`, `credits.md`, or `troubleshooting.md`.

## Human review checklist

- [ ] Split recommendations match real reader journeys
- [ ] No page contains more than one primary Diátaxis type
- [ ] Reference pages have no narrative paragraphs longer than one sentence
- [ ] Tutorial keeps a single learning path without turning into full API reference
