---
title: Dynamic update scenarios
type: process
audience: Technical writers, engineers, and product managers
status: draft
---

# Dynamic update scenarios

This document shows how the [documentation lifecycle](./lifecycle.md) works when the product changes over time. Each scenario uses the PDF.co merge sample docs as a concrete example, but the process applies to any topic.

Scenarios are **illustrative**. They describe hypothetical product or reference changes. Reader-facing docs under `pdfco-merge/` reflect the current [official API reference](https://developer.pdf.co/api/merge/pdf) unless you are actively executing a scenario update.

## How to read these scenarios

Every scenario follows the same structure:

| Field | Description |
|---|---|
| **Trigger** | Signals that show the documentation may be out of date. |
| **What changed** | The product or source change. |
| **Docs affected** | Reader-facing files that need updates. |
| **AI steps** | Which steps from the [AI-assisted workflow](./ai-assisted-docs-workflow.md) run. |
| **Human gates** | Required review before merge. |
| **Approval** | Who signs off and how the change is completed. |

AI step names match the drafting loop. Each scenario lists all five steps. Steps marked **Not required** are intentionally skipped for that change type.

1. Define reader outcome.
2. Extract facts into a reviewable table.
3. Generate a constrained first draft.
4. Run automated checks.
5. Perform human review at the risk boundary.

---

## Scenario 1: Minor terminology change

### Trigger

PDF.co updates the `url` attribute description on the [Merge PDF API reference](https://developer.pdf.co/api/merge/pdf). The new text says to pass multiple sources as a **comma-separated string**. The older text said to **separate URLs with a comma**. Field name, endpoint (`POST /v1/pdf/merge`), and behavior are unchanged.

A writer notices the mismatch during a quarterly source audit, as this change does not affect functionality and would not be caught through an increase in support tickets, for example.

### What changed

Wording only in the official reference file. No new endpoints, fields, or API Tester flows.

### Docs affected

| File | Change needed |
|---|---|
| `pdfco-merge/merge-reference.md` | Align the `url` row description with the updated reference wording. |
| `pdfco-merge/troubleshooting.md` | Align the validation checklist bullet about comma-separated URLs (if phrasing differs). |

**Not affected:** `merge-pdfs.md` (already used "comma-separated string" within the tutorial), `credits.md`.

### AI steps

| Step | Action |
|---|---|
| **1. Define reader outcome** | Not required. Reader outcome is unchanged. |
| **2. Extract facts** | Optional: AI diffs old vs new reference text for the `url` attribute only. |
| **3. Constrained draft** | AI proposes updated phrasing in affected tables and bullets, using verified wording only. |
| **4. Automated checks** | Terminology lint: flag outdated phrase "separate with a comma" in other files, if necessary. |
| **5. Human review** | Writer confirms no endpoint paths or field names were altered. |

### Human gates

- **Cross-link check:** Confirm updated language matches the live [Merge PDF API reference](https://developer.pdf.co/api/merge/pdf).
- **Risk level:** Low. No SME fact-check on API behavior required.

### Approval

- Writer merges after CI passes and self-review.
- Close the linked issue with: "Terminology sync — `url` attribute description only. No API behavior change."

---

## Scenario 2: Major feature launch

### Trigger

PDF.co adds a new profile option `MergePageRanges` to `POST /v1/pdf/merge`, allowing selective page merging from each source PDF. Engineering posts the API reference update. Product team requests that documentation be completed in time for launch.

### What changed

New optional profile attribute with documented syntax, defaults, and an API Tester example. Existing merge behavior unchanged for users who omit the field.

### Docs affected

| File | Change needed |
|---|---|
| `pdfco-merge/merge-reference.md` | Add `MergePageRanges` to the profiles table under a new or existing section. |
| `pdfco-merge/troubleshooting.md` | Add new section about merge fail due to invalid range syntax. |
| `process/topic-brief.md` | Update reader outcomes only if selective merge changes what the doc set promises. |

**Not affected:** `merge-pdfs.md` (tutorial scope excludes profiles), `credits.md`.

### AI steps

| Step | Action |
|---|---|
| **1. Define reader outcome** | Writer adds to issue: "Reader can pass `MergePageRanges` in a merge request and interpret the response." |
| **2. Extract facts** | AI extracts field name, type, default, syntax, and limits from updated API reference only. |
| **3. Constrained draft** | AI drafts reference table row and optional troubleshooting symptom using verified fact table. |
| **4. Automated checks** | Field name lint: `MergePageRanges` must appear in fact table. Complete link check on new cross-references. |
| **5. Human review** | Engineer or writer validates syntax with API Tester. The technical writer copy-edits draft. |

### Human gates

- **SME fact-check:** Required. Run a test request in API Tester with `MergePageRanges` and confirm response matches docs.
- **IA check:** New content stays in reference (lookup), it should not be added to existing tutorial.
- **Cross-link check:** Reference links from troubleshooting. 

### Approval

- Writer merges after engineer confirms API Tester result.
- Product owner optional sign-off if launch is customer-facing.
- Close issue with API reference version link and API Tester screenshot attached.

---

## Scenario 3: Outdated, incomplete, or actively conflicting information

### Trigger

The [PDF.co merge API reference](https://developer.pdf.co/api/merge/pdf) and production API already reflect a behavior change: when `async` is `true`, the **initial response no longer includes `url`**. The client receives `jobId`, `status`, and `error` only. The output `url` appears in the [Background Job Check](https://developer.pdf.co/api/job-check) response or the `callback` payload after the job completes.

The sample docs in this repository were not updated when the API shipped. Support tickets increase for async merge integrations. Readers follow local docs that still describe the old contract.


### What changed (and what conflicts)

| Source | State |
|---|---|
| **Live API and official reference** | Initial async response returns `jobId` only. `url` appears after job completion. |
| **Local docs (`pdfco-merge/`)** | Still imply `url` may appear in the initial async response. |
| **Conflict type** | Actively conflicting. Tutorial and reference contradict production behavior. |
| **Reader impact** | Integrations fail. Support load increases. Trust in docs erodes until corrected. |

Synchronous responses (`async: false`) are unchanged. The conflict is isolated to async handoff documentation.

### Docs affected

| File | Change needed |
|---|---|
| `pdfco-merge/merge-reference.md` | Remove `url` from initial async response table. Document where `url` appears after completion. |
| `pdfco-merge/merge-pdfs.md` | Correct async section. Workflow must wait for Job Check or callback before using `url`. |
| `pdfco-merge/troubleshooting.md` | Prominent correction for "expects URL but receives `jobId`". Tie to behavior change date. |

**Not affected:** `credits.md` (credit fields unchanged).


### AI steps

| Step | Action |
|---|---|
| **1. Define reader outcome** | Writer updates issue outcome: "Reader can run an async merge and retrieve `url` only after job completion." |
| **2. Extract facts** | AI diffs **local docs vs live API reference and API Tester captures**. Flags every stale or conflicting claim. Must not carry forward the old `url`-on-first-response assumption. |
| **3. Constrained draft** | AI **removes** wrong statements and **adds** corrected prose in reference, tutorial, and troubleshooting. Correction framing in troubleshooting ("behavior changed on [date]"). |
| **4. Automated checks** | Field lint: async response section must not list `url` as initial-response field. Link check on Job Check endpoint cross-references. |
| **5. Human review** | **Required.** Engineer validates against **production** API Tester: initial async response shape, then check Job Check response contains `url`. Support reviews troubleshooting against real ticket language. |

### Human gates

- **SME fact-check:** Required against production. Attach initial async response and completed Job Check endpoint response to the issue.
- **Conflict resolution:** Local docs must match live API before merge. If reference and API Tester disagree, block until product owner resolves.
- **Support liaison:** Confirm troubleshooting updates address reported failure patterns.
- **Risk level:** High. Docs currently mislead integrators. Merge is a correction, not a preview.

### Approval

- Writer merges as soon as validation completes.
- Engineer confirms both response shapes in production API Tester.
- Support acknowledges public docs now match production behavior.
- Issue closed with: date docs matched production, link to postmortem or ticket count, and list of conflicting statements removed.

---

## Scenario comparison

| Scenario | Risk | AI draft useful? | SME required? | Typical approver |
|---|---|---|---|---|
| Attribute description wording (`url`) | Low | Yes (targeted phrasing sync) | No | Writer |
| Major feature launch | Medium | Yes (reference extraction + draft) | Yes | Writer + engineer |
| Outdated docs vs live API (async contract) | High | Yes (diff, remove conflicts, multi-page fix) | Yes | Writer + engineer + support |

## Preventing drift between audits

| Practice | Frequency | Owner |
|---|---|---|
| Link check on external URLs (API reference, pricing, response codes) | Every PR + weekly CI | Automated |
| API Tester smoke test on documented example request | On API reference change + quarterly | Writer or engineer |
| Terminology lint against banned/outdated terms | Every PR | Automated |
| Support ticket review for recurring doc gaps | Monthly | Writer + support |

## Related documents

- [End-to-end documentation lifecycle](./lifecycle.md): Full pipeline and Definition of Done.
- [AI-assisted docs workflow](./ai-assisted-docs-workflow.md): Drafting loop and source-of-truth policy.
- [Information architecture](./information-architecture.md): Which page type absorbs which kind of change (component 3).
- [PDF.co merge sample](../pdfco-merge/README.md): Reader-facing docs referenced in these scenarios.
