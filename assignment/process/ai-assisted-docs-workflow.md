---
title: Docs-as-code and generative AI workflow
type: process
audience: Technical writers and documentation maintainers
status: draft
---

# Docs-as-code and generative AI workflow

This document describes a repeatable documentation workflow that applies to an API topic. It was created and tested against the PDF.co merge endpoint. In this workflow, generative AI acts as an accelerator, not as the authority.

See also: [End-to-end documentation lifecycle](./lifecycle.md).

## Keep the source of truth explicit

AI agents work efficiently with explicit and restrictive sources of truth. Hallucinations often stem from loose or broad requests. For the PDF.co merge sample in this assignment, I used the following source hierarchy:

1. **PDF.co API reference:** request fields, defaults, limits, and response fields.
2. **PDF.co API Tester:** executable validation of a request and a practical credit estimate.
3. **PDF.co credit-pricing page:** current rates and account-level billing information.
4. **Reader-facing Markdown pages** (for example, under `pdfco-merge/`): task-oriented explanations, examples, and troubleshooting guidance.

If sources disagree, the AI agent is instructed to surface the discrepancies to the writer, instead of choosing silently.

## Skills and rules

Workflow skills live in [process/ai/skills/](ai/skills/). Each skill is a directory with a `SKILL.md` file, following the [Cursor Agent Skills](https://cursor.com/docs/agent/skills) format used by Claude and Cursor. Style constraints live in [process/ai/rules/pdfco-style-guide.md](ai/rules/pdfco-style-guide.md). Rules are separate from skills: rules constrain output globally, skills define step-specific workflows.

### How to use skills in Cursor or Claude

| Action | Detail |
|---|---|
| **Install for a project** | Copy or symlink `process/ai/skills/*` into `.cursor/skills/` at the repository root so the editor can discover them. |
| **Invoke a skill** | Name the skill in chat (for example, "use the extract-facts skill") or attach `@extract-facts` where the editor supports skill references. |
| **Provide inputs** | Paste the source reference, fact table, or draft Markdown described in each skill's "Input the writer provides" section. |
| **Review output** | Run the skill's human review checklist before moving to the next pipeline step. |

Skills use `disable-model-invocation: true` so they load only when explicitly invoked, not on every request.

### What this submission includes

This assignment delivers the **workflow design**: skills, style rules, and process documentation committed under `process/`. It does not include CI workflows, or lint configuration. 


## Pipeline overview

| Step | Who | What must be done | Outcome |
|---|---|---|---|
| **Prepare sources** | Writer | Collect the official API reference, run API Tester, and note any conflicts between sources. | Verified source material ready for extraction. |
| **1. Define reader outcome** | Writer | Write one testable "after reading this page…" sentence per file. State what the page does not cover. | Scope sentence per page ([reader-outcome](ai/skills/reader-outcome/SKILL.md) skill). |
| **2. Extract facts** | AI, then writer | Invoke `extract-facts`. Writer diffs the table against the live reference and API Tester. | Reviewable fact table ([extract-facts](ai/skills/extract-facts/SKILL.md) skill). |
| **3. Constrained first draft** | AI, then SME | Invoke `constrained-draft` with approved facts and reader outcome. SME validates endpoints, fields, and async handoff. | First-draft page under `pdfco-merge/` ([constrained-draft](ai/skills/constrained-draft/SKILL.md) skill). |
| **Diátaxis structuring** *(optional)* | AI, then writer | Invoke `structure-and-diataxis`. Writer confirms the structure matches reader journeys. | Restructured draft or split plan ([structure-and-diataxis](ai/skills/structure-and-diataxis/SKILL.md) skill). |
| **4. Automated checks** | CI, then writer *(production)* | Run link, lint, front matter, and field-name checks on every change. Writer fixes failures. | CI pass with actionable lint output. Manual equivalent until CI is configured. |
| **5. Human review** | Writer, SME, support *(as needed)* | Verify high-impact claims: examples, async contract, credits, security, and workflow fit. | Approved Markdown ready to merge. |
| **Editorial review** *(optional)* | AI, then writer | Invoke `editorial-review`. Verify URLs, media, and basic accessibility. Writer reviews and applies diff hunks selectively. | Annotated findings and unified diff ([editorial-review](ai/skills/editorial-review/SKILL.md) skill). |
| **Publish** | Writer | Commit and merge to Git after gates pass. | Documentation in version control. |

Steps 1–5 match the drafting loop referenced in [dynamic update scenarios](./dynamic-updates.md). Diátaxis structuring and editorial review are optional passes between draft and publish.

## The five drafting steps

### Step 1: Define the reader outcome

| | |
|---|---|
| **Responsibility** | State one testable outcome per page before AI drafting |
| **Inputs** | [Assignment README](../README.md) reader goals, target page type, scope boundaries |
| **Outputs** | One-sentence reader outcome worksheet per page |
| **Skill** | [`reader-outcome`](ai/skills/reader-outcome/SKILL.md) |
| **Human review** | **Required.** Writer confirms outcome matches page type and assignment scope |
| **Risks caught** | Generic API summaries, scope creep into reference-only topics, tutorials that promise options not in scope |

Example outcome:

> After reading this page, an automation engineer can send a valid merge request, recognize a successful response, and choose synchronous or asynchronous processing.

### Step 2: Extract facts into a reviewable table

| | |
|---|---|
| **Responsibility** | Extract API facts from the official reference without invention |
| **Inputs** | Pasted [Merge PDF API reference](https://developer.pdf.co/api/merge/pdf), optional API Tester capture |
| **Outputs** | Markdown fact table with source references |
| **Skill** | [`extract-facts`](ai/skills/extract-facts/SKILL.md) |
| **Human review** | **Required.** Writer diffs table against live reference and API Tester |
| **Risks caught** | Hallucinated fields, wrong defaults, stale async response shape, inferred credit rates |

The model is useful for coverage and consistency. It does not replace the writer's comparison with the live source.

### Step 3: Generate a constrained first draft

| | |
|---|---|
| **Responsibility** | Turn the approved fact table into a page-type-appropriate Markdown draft |
| **Inputs** | Approved fact table, reader outcome, target filename under `pdfco-merge/`, [style guide](ai/rules/pdfco-style-guide.md) |
| **Outputs** | First-draft Markdown with front matter, examples, and cross-links |
| **Skill** | [`constrained-draft`](ai/skills/constrained-draft/SKILL.md) |
| **Human review** | **Required.** SME checks endpoints, fields, async handoff (Job Check / callback), and limits |
| **Risks caught** | Invented error codes, wrong merge vs merge2 scope, marketing tone, missing async workflow steps |

#### Optional: Diátaxis structuring

| | |
|---|---|
| **Responsibility** | Fix tutorial/reference/troubleshooting bleed or recommend a split |
| **Inputs** | Draft Markdown, declared page type |
| **Outputs** | Restructured draft or split plan across `pdfco-merge/` files |
| **Skill** | [`structure-and-diataxis`](ai/skills/structure-and-diataxis/SKILL.md) |
| **Human review** | **Required.** Confirm split matches reader journeys |
| **Risks caught** | Field tables inside tutorials, numbered steps on reference pages, duplicated credit exposition |

### Step 4: Run automated checks

| | |
|---|---|
| **Responsibility** | Validate objective, machine-checkable rules on every change |
| **Inputs** | Markdown files under `pdfco-merge/`, reviewed fact table or field list |
| **Outputs** | CI pass or fail with actionable lint messages |
| **Skill** | None. Configure linters in the docs repository when moving to production. |
| **Human review** | **Light.** Writer fixes failures. No SME required unless a lint exposes a factual mismatch |
| **Risks caught** | Broken links, missing front matter, secret leakage, field-name drift from contract |

These are the checks a production repository should run automatically. For this assignment submission, run the equivalent checks manually before merge:

- Markdown files parse successfully.
- Every page has front matter with `title`, `type`, `audience`, and `status`.
- Code fences have a language identifier.
- External links use HTTPS where required.
- API paths and field names match the reviewed contract.
- No example contains a real API key, password, or session token.
- Credit rates from illustrative examples are labeled as changeable.

These checks catch drift and accidental secret exposure. They cannot decide whether an explanation is clear or whether a workflow is safe.

### Step 5: Perform human review

| | |
|---|---|
| **Responsibility** | Verify high-impact claims before merge. |
| **Inputs** | Draft after automated checks, API Tester results, support context if applicable. |
| **Outputs** | Approved Markdown ready to merge. |
| **Skill** | None for SME review. Skill [`editorial-review`](ai/skills/editorial-review/SKILL.md) for prose polish. |
| **Human review** | **Required.** Writer plus SME for API behavior. Support liaison when correcting stale docs. |
| **Risks caught** | Wrong async contract, unsafe retry advice, credential exposure, incorrect credit claims. |

A human writer or subject-matter expert must verify:

- Required attributes, defaults, case-sensitive names, and size limits.
- Request and response examples against the live API Tester.
- Async behavior and Job Check / callback completion paths.
- Credit and pricing statements.
- Storage, expiration, privacy, and credential guidance.
- Error explanations and retry advice.
- Whether the article matches the reader's actual workflow.

The highest-risk statements are the ones that can waste money, lose a document, expose credentials, or cause a workflow to retry indefinitely.

#### Editorial review

| | |
|---|---|
| **Responsibility** | Verify links, media, and basic accessibility. Enforce [style guide](ai/rules/pdfco-style-guide.md) without changing API facts. |
| **Inputs** | SME-approved draft, style guide, repository file tree for relative link checks |
| **Outputs** | Link inventory, accessibility findings, annotated suggestions, and unified diff |
| **Skill** | [`editorial-review`](ai/skills/editorial-review/SKILL.md) |
| **Human review** | **Light.** Author spot-checks external URLs and accepts or rejects each suggestion |
| **Risks caught** | Hallucinated links, sensory-only instructions, missing alt text, vague link text, broken heading hierarchy, inconsistent terminology |

## Human-in-the-loop gates

These gates are non-negotiable for API documentation in this sample:

1. **Fact-table diff** (after step 2): Every field matches the official reference or API Tester capture.
2. **SME fact-check** (after step 3): No AI draft merges without endpoint and response validation.
3. **Cross-link check**: Each page links to its logical next step. Reference links from troubleshooting where symptoms mention fields.
4. **Risk sign-off** (step 5): High-risk changes (async contract, credits, security) need engineer confirmation. See [dynamic update scenarios](./dynamic-updates.md).

## What AI is good at here

Generative AI can speed up:

- Turning a fact table into several articles.
- Finding duplicated explanations across pages.
- Suggesting headings and examples for different audiences.
- Checking that each requested outcome appears somewhere in the draft.
- Producing a first-pass changelog or release-note summary from a diff.
- Generating test cases for documentation linting.

## What still needs an accountable human

Generative AI should not be the final authority for:

- Undocumented API behavior.
- Current pricing or credit consumption.
- Security and credential handling.
- Data retention or privacy claims.
- Error-code meanings.
- Whether a retry is safe.
- Compatibility between file types and account modes.

The writer owns the decision to publish. The model can make the path to that decision faster and more systematic.

## Tooling

| Tool | Role |
|---|---|
| Cursor / Claude | Steps 2–3 and optional structure/editorial passes via [skills/](ai/skills/) |
| [pdfco-style-guide.md](ai/rules/pdfco-style-guide.md) | Style constraints for drafting and editorial review |
| [PDF.co API reference](https://developer.pdf.co/api/merge/pdf) | Source of truth for extraction and SME diff |
| [API Tester](https://developer.pdf.co/api-tester/merge/pdf) | Executable validation |
| Git | Version control, diff review, and collaboration |

## Change detection and maintenance

For a production repository, schedule a lightweight review when:

- The API reference changes.
- The API Tester request or response shape changes.
- Credit-pricing information changes.
- A response-code page changes.
- A new file type or merge mode is introduced.
- Support tickets reveal a repeated misunderstanding.

The maintenance issue should link the source change to the affected article and include a small verification task. This is more reliable than asking an AI model to keep the docs up to date without a source diff or acceptance criteria.

See [dynamic update scenarios](./dynamic-updates.md) for three worked examples (terminology sync, feature launch, stale docs vs live API).

## Related documents

- [End-to-end documentation lifecycle](./lifecycle.md): trigger through publish, Definition of Done.
- [Dynamic update scenarios](./dynamic-updates.md): how the five steps apply when the product changes.
- [Information architecture](./information-architecture.md): which page type absorbs which kind of change.
- [PDF.co merge sample](../pdfco-merge/README.md): reader-facing pages produced by this workflow.
