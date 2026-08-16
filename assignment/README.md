# Technical Writing Assignment — Submission

**Assignment:** develop and document a docs-as-code process, including samples and process files.

**Starting point for end users:** [pdfco-merge/README.md](pdfco-merge/README.md)

**Assignment context:** [process/topic-brief.md](process/topic-brief.md)

## Assignment map

| # | Component | Status | Location |
|---|---|---|---|
| 1 | Topic selection | Done | [process/topic-brief.md](process/topic-brief.md) |
| 2 | Sample documentation | Done | [pdfco-merge/](pdfco-merge/): 4 Markdown pages |
| 3 | Information architecture | Done | [process/information-architecture.md](process/information-architecture.md) |
| 4 | AI-assisted workflow | Done | [process/ai-assisted-docs-workflow.md](process/ai-assisted-docs-workflow.md) |
| 5 | End-to-end lifecycle | Done | [process/lifecycle.md](process/lifecycle.md) |
| 6 | Dynamic update scenarios | Done | [process/dynamic-updates.md](process/dynamic-updates.md) |
| 7 | Video walkthrough | Pending | Link to be added |

## Repository layout

```
.
├── README.md                 ← you are here (submission index)
├── process/                  ← reusable workflow (any endpoint)
│   ├── topic-brief.md
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

- Plain Markdown in Git: This format can be easily converted into .mdx to use further components, if required by platforms such as Mintlify.
- [PDF.co official API reference](https://developer.pdf.co/api/merge/pdf) is the source of truth for behavior in the sample docs.
- Process documentation lives at the repository root in `process/` and applies to any API topic, not only PDF.co merge.
