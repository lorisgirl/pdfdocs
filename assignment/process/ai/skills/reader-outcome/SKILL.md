---
name: reader-outcome
description: Defines testable reader outcomes and scope boundaries for API documentation pages. Use before AI drafting, when scoping a new page, splitting content, or reviewing whether a page promises too much.
disable-model-invocation: true
---

# Define reader outcome

Run this step before any generative AI drafting. It is usually a human writer task. An AI agent can help refine wording when this skill is invoked, but the writer owns the outcome.

## Writer worksheet

Fill in one sentence per page:

```
Page: {filename, for example pdfco-merge/merge-pdfs.md}
Page type: {tutorial | reference | explanation | troubleshooting}
Audience: Operations managers and automation engineers who understand REST and cURL.

After reading this page, the reader can:
{one concrete, testable outcome}

This page does NOT promise:
{scope boundaries, for example "detailed instructions on using page merge profiles"}
```

## Example (merge tutorial)

```
Page: pdfco-merge/merge-pdfs.md
Page type: tutorial
Audience: Operations managers and automation engineers who understand REST and cURL.

After reading this page, the reader can:
Send a valid merge request with cURL, read a synchronous response, and choose async processing with Job Check or callback for larger jobs.

This page does NOT promise:
Profile options, credit-rate guarantees, or multi-format merge (merge2 endpoint).
```

## When the writer asks for AI assist

Review the draft outcome the writer provides:

```
Page type: {tutorial | reference | explanation | troubleshooting}
Draft outcome: {paste one sentence}
```

Check:

- Is the outcome testable (reader can verify success)?
- Does it match the page type (tutorial = do, reference = lookup)?
- Does it avoid scope creep into reference-only topics?

Suggest one revised sentence if needed. Do not add API facts not in the source.

## Human review checklist

- [ ] Outcome is one sentence and testable
- [ ] Outcome matches the Diátaxis page type
- [ ] Scope boundaries are explicit where the official reference is large
- [ ] Outcome aligns with [topic brief](../../../topic-brief.md) reader goals
