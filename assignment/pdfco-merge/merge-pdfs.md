---
title: Merge PDF files
type: tutorial
audience: Operations managers and automation engineers
status: draft
---

# Merge PDF files

Use the PDF.co merge operation to combine multiple source files into one output document. This tutorial shows how to make a synchronous request with `curl`, interpret the response, and choose an asynchronous request for longer jobs.

## What you will build

You will send two PDF URLs to:

```text
POST https://api.pdf.co/v1/pdf/merge
```

The service returns a link to the merged PDF and metadata that your automation can pass to the next step in a workflow.

## Prerequisites

You need:

1. A PDF.co account and API key.
2. [`curl`](https://everything.curl.dev/index.html) installed on your machine.
3. PDF file URLs that PDF.co can access.

> Note: The API key belongs in the `x-api-key` header. Do not put it in the JSON body or commit it to a script. For a general introduction to authentication, read the [PDF.co API documentation](https://developer.pdf.co/api).

## Merge two PDFs

The `url` field is required. Pass multiple source URLs as a comma-separated string. The order of the URLs determines the order of the source documents in the merged output.

To get started, replace `YOUR_API_KEY` with the value you copied from your PDF.co account. 

```bash
curl --location --request POST 'https://api.pdf.co/v1/pdf/merge' \
  --header 'x-api-key: YOUR_API_KEY' \
  --header 'Content-Type: application/json' \
  --data-raw '{
    "url": "https://pdfco-test-files.s3.us-west-2.amazonaws.com/pdf-merge/sample1.pdf,https://pdfco-test-files.s3.us-west-2.amazonaws.com/pdf-merge/sample2.pdf",
    "async": false
  }'
```

Using curl, send the request and check that you receive a JSON response from the API.


## Inspect the response

A successful response has `error: false` and a `status` value of `"200"`. A typical response looks like this:

```json
{
  "url": "https://pdf-temp-files.s3.amazonaws.com/example/result.pdf",
  "error": false,
  "status": "200",
  "name": "result.pdf",
  "pageCount": 4,
  "credits": 8,
  "remainingCredits": 98465,
  "duration": 1830,
  "outputLinkValidTill": "2026-08-15T12:00:00Z"
}
```
### Understand response fields

* The returned `url` is a temporary output link, but you can configure different storage strategies in your account. 
* The field `outputLinkValidTill` displays the temporary file expiry timestamp. 
* The duration depends on your subscription plan and what you set in the `expiration` attribute of your request. Learn more about request attributes in the [Merge PDF API reference](./merge-reference.md).

## What you can do with the response

Once your request is processed, a downstream automation, such as Make or Zapier, can use the response to continue a business processes, such as storing invoice copies for bookkeeping:

1. Check `status` is `200`.
2. If check pass, pass `url` to the next workflow step.
3. Record `credits` and `remainingCredits` in a spreadsheet for monitoring. Learn more about [how credits are charged](./credits.md).
4. Save the output file to a permanent location.
5. Alert Accounting team in Slack that a new invoice has been stored.

## (Optional) Use asynchronous processing for longer jobs

Synchronous processing is convenient for a short test. Asynchronous processing allows you to better handle a client or platform timeout. To submit an asynchronous request, set `async` to `true`:

```json
{
  "url": "https://example.com/first.pdf,https://example.com/second.pdf",
  "async": true,
  "callback": "https://automation.example.com/hooks/pdfco-merge"
}
```

With `async: true`, PDF.co processes the request in the background. The response includes a `jobId` and may include a `url` and other fields. Use the [Background Job Check endpoint](https://developer.pdf.co/api/job-check) or a `callback` URL to confirm the job completed before you use the output. Learn more in the [Merge PDF API reference](./merge-reference.md).

Your workflow should treat the merge as incomplete until the Job Check response or callback reports completion. If the request times out, read [Fix errors in file merge actions](./troubleshooting.md).


## Related pages

- [Merge PDF API reference](./merge-reference.md).
- [Understand how credits are charged](./credits.md).
- [Fix errors in file merge actions](./troubleshooting.md).
