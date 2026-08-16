---
title: Understand how credits are charged for merge actions
type: explanation
audience: Operations managers and automation engineers
status: draft
---

# Understand how credits are charged for merge actions

PDF.co reports credit consumption as part of the merge response. Use that value for operational monitoring, while using the live PDF.co pricing information for current planning and budgeting.

## What determines credit usage?

The amount of credit used depends on two inputs:

1. The number of pages.
2. The PDF.co API or function being used.

For current rates, check the [PDF.co credit-pricing page](https://app.pdf.co/subscriptions).

## Fields available for credit tracking

Every response provides two credit-related fields:

| Field | Meaning |
| --- | --- |
| `credits` | Credits consumed by the request. |
| `remainingCredits` | Credits remaining in the account after the request. |

You can record these fields to monitor usage of your account credits.

Example:

```json
{
  "url": "https://pdf-temp-files.s3.amazonaws.com/example/result.pdf",
  "error": false,
  "status": "200",
  "pageCount": 12,
  "credits": 24,
  "remainingCredits": 9976
}
```

For more response field definitions, read [Merge PDF API reference](./merge-reference.md).

> Note: The values in this example illustrate the response structure, not a guaranteed rate. 


## Estimate before building

PDF.co offers an [API Tester](https://developer.pdf.co/api-tester/merge/pdf), where you can estimate the credit usage. You must use the same file sizes, page counts, and operation mode as your workflow.

The formula for credit usage is:

```text
expected pages × current credits-per-page rate = estimated credits
```

For example, if you submit a merge request for 12 pages, using the illustrative rate of 2 credits per page:

```text
12 pages × 2 credits per page = 24 credits
```

Treat the result of the formula as an estimate when adding it to planning documents and dashboards.


## What to do when the balance is low

If your account has a low balance, you'll not be able to send requests to the endpoint that surpass your current available credits. You can:

1. Select a different plan or credit-pack to extend your usage. Learn about the [different plan types](https://app.pdf.co/subscriptions).
2. Pause non-critical, retry-heavy workflows.
3. Preserve any failed request context for investigation on how to improve performance.

> Note: The `remainingCredits` value is useful for runtime visibility, but may be outdated if multiple requests happen in parallel. 
> Use the account dashboard and subscription pages as the source of truth for plan-level limits and purchasing decisions.

## Related pages

- [Merge PDF files](./merge-pdfs.md).
- [Merge PDF API reference](./merge-reference.md).
- [Fix errors in file merge actions](./troubleshooting.md).
