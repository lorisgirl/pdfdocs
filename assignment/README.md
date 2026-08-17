# Technical Writing Assignment — Submission

**Assignment:** Develop and document a docs-as-code process, including samples and process files.

**Starting point for end users:** [pdfco-merge/README.md](pdfco-merge/README.md)

## Topic selection

**Topic:** PDF.co `POST /v1/pdf/merge`, which combines multiple PDF source files into one output PDF.

**Audience:** Operations managers and automation engineers who integrate PDF.co into business workflows (Zapier, Make, custom scripts, or internal tools).

### Problem this documentation solves

- A structured path from first successful request through operational monitoring and failure diagnosis.
- Solutions for failing requests.
- A solid reference for endpoint attributes in requests and responses.
- Better awareness of how credits are used with this endpoint.

### What the reader can do after reading

1. Send a valid merge request with `curl` and read the response.
2. Choose synchronous or asynchronous processing for their job size.
3. Look up request and response fields without reading the full official reference.
4. Estimate and monitor credit usage using response fields and live pricing.
5. Diagnose common failures before they interrupt a production workflow.

### Prerequisites

- A PDF.co account and API key.
- Basic REST and `curl` familiarity.
- Source PDF files available at URLs PDF.co can reach over HTTPS.

## Assignment map

| # | Component | Status | Location |
|---|---|---|---|
| 1 | Topic selection | Done | This document |
| 2 | Sample documentation | Done | [pdfco-merge/](pdfco-merge/): 4 Markdown pages |
| 3 | Information architecture | Done | [process/information-architecture.md](process/information-architecture.md) |
| 4 | AI-assisted workflow | Done | [process/ai-assisted-docs-workflow.md](process/ai-assisted-docs-workflow.md) |
| 5 | End-to-end lifecycle | Done | [process/lifecycle.md](process/lifecycle.md) |
| 6 | Dynamic update scenarios | Done | [process/dynamic-updates.md](process/dynamic-updates.md) |
| 7 | Video walkthrough | Done | Submitted by email |

## Repository layout

```
.
├── README.md                 ← you are here (submission index + topic selection)
├── process/                  ← reusable workflow (any endpoint)
│   ├── information-architecture.md
│   ├── lifecycle.md
│   ├── dynamic-updates.md
│   ├── ai-assisted-docs-workflow.md
│   └── ai/
│       ├── skills/           ← five Agent Skills (SKILL.md per directory)
│       └── rules/
│           └── pdfco-style-guide.md
└── pdfco-merge/              ← reader-facing sample (this assignment)
    ├── README.md             ← doc index for readers
    ├── merge-pdfs.md
    ├── merge-reference.md
    ├── credits.md
    └── troubleshooting.md
```

## Docs-as-code approach

- Plain Markdown in Git. This format can convert to MDX for platforms such as Mintlify.
- The [PDF.co official API reference](https://developer.pdf.co/api/merge/pdf) is the source of truth for behavior in the sample docs.
- Process documentation lives in `process/` and applies to any API topic, not only PDF.co merge.
