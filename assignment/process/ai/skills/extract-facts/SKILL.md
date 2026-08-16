---
name: extract-facts
description: Extracts API facts from official reference text into a reviewable Markdown table without invention. Use when building or updating API documentation from the PDF.co merge reference or after an API reference change.
disable-model-invocation: true
---

# Extract facts

Build a reviewable fact table from the official PDF.co merge API reference. Extraction reduces hallucination in later drafting steps.

## Instructions

Extract facts only. Do not invent fields, defaults, limits, pricing, or behavior. If the source does not state a value, write `not stated`.

Preserve field names, endpoint paths, and types exactly as in the source.

Using only the source text the writer provides, extract:

- Endpoint and HTTP method
- Request attributes (name, type, required, default, description)
- Input limits
- Response fields for synchronous and asynchronous modes
- Official examples
- Linked troubleshooting topics

For every extracted fact, include a source heading or line reference.

Do not infer billing rates, retries, authentication behavior, or retention rules.

## Input the writer provides

```
Source:
<paste the current official Merge PDF API reference here>
```

## Expected output

A Markdown fact table the writer can diff against the live reference and API Tester. Example shape:

| Fact | Value | Source |
|---|---|---|
| Endpoint | `POST https://api.pdf.co/v1/pdf/merge` | Endpoint section |
| `url` required | Yes | Request attributes |
| `async` default | `false` | Request attributes |

## Human review checklist

- [ ] Every field name matches the [official reference](https://developer.pdf.co/api/merge/pdf)
- [ ] Defaults and limits match the reference (not inferred)
- [ ] Async response shape distinguishes initial response from Job Check completion
- [ ] No credit rates copied unless explicitly stated in the pasted source
- [ ] Writer ran at least one request in [API Tester](https://developer.pdf.co/api-tester/merge/pdf) for behavior the reference does not spell out
