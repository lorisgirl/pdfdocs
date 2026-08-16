---
title: Fix errors in file merge actions
type: troubleshooting
audience: Operations managers and automation engineers
status: draft
---

# Fix errors in file merge actions

When a merge action fails, diagnose the request in this order:

1. Request fields
2. Source-file access
3. Size
4. Processing time
5. Downstream handling

This allows you to quickly find the spot where the request is failing.

If you have not completed a merge request yet, start with [Merge PDF files](./merge-pdfs.md). For field definitions, read [Merge PDF API reference](./merge-reference.md).

## Start with the response

Inspect these fields:

| Field | What it tells you | Possible values |
| --- | --- | --- |
| `error` | Whether PDF.co reports an error. | `true` = error. `false` = success. |
| `status` | Status code for the request. | Values such as `200`, `404`, or `500`. Documented in [Response codes](https://developer.pdf.co/api/response-codes). |
| `url` | Output file URL when the merge succeeds. | HTTPS URL to temporary storage. |
| `pageCount` | Pages in the merged output. | Positive integer. |
| `credits` | Credits consumed by the request. | Positive integer. |
| `remainingCredits` | Credits remaining after the request. | Non-negative integer. |

For status-specific behavior, use the [PDF.co response codes reference](https://developer.pdf.co/api/response-codes). Do not retry every failure automatically. A malformed request or inaccessible source URL will fail again and may waste processing time or credits.

## Request validation checklist

Check these items before investigating the source files. For full field definitions, read [Merge PDF API reference](./merge-reference.md).

- The method is `POST`.
- The path is `/v1/pdf/merge`.
- The body is valid JSON.
- The `Content-Type` header is `application/json`.
- The API key is present in the `x-api-key` header.
- The attribute name is exactly `url`. Request attributes are case-sensitive.
- `url` is a string, not a JSON array.
- Multiple URLs are separated by commas.

This is a valid minimal body:

```json
{
  "url": "https://example.com/first.pdf,https://example.com/second.pdf"
}
```

## The source URL cannot be read

PDF.co needs to retrieve each source URL. A URL can fail even when it opens in
your browser, for example when it:

- Requires a login or session cookie.
- Is available only on a private network.
- Redirects to an unsupported location.
- Has expired.
- Returns an HTML error page instead of a document.
- Blocks the service's request.

Test each source independently. Confirm that the URL is accessible without
your browser session and that it points to the intended file. If the source
requires HTTP Basic Authentication, use `httpusername` and `httppassword` as
documented request fields. Do not include credentials in a URL that may be
logged or copied into an issue.

For sensitive or reusable files, consider PDF.co's file-upload or built-in
file-storage options instead of generating short-lived links in multiple
systems.

## The request exceeds the input-size limit

The combined size of all input file URLs must not exceed 2 GB. The limit applies
to the request as a whole, not just to one source file.

If the request is too large:

1. Measure the size of every input.
2. Remove duplicate or unnecessary files.
3. Split the workflow into smaller merge jobs.
4. Use asynchronous processing for longer jobs.
5. Join the resulting documents in a later workflow step if that matches the
   business requirement.

Do not keep retrying an oversized request. It cannot succeed until the input
set changes.

## The request times out

Large or complex merges may take longer than a synchronous client or
automation platform allows. Set `async` to `true` and then use the returned
`jobId` with the
[Background Job Check endpoint](https://developer.pdf.co/api/job-check), or
configure a callback URL.

Example:

```json
{
  "url": "https://example.com/large-file-a.pdf,https://example.com/large-file-b.pdf",
  "async": true,
  "callback": "https://automation.example.com/hooks/pdfco-merge"
}
```

The callback field is used only when `async` is `true`. Design the callback
handler to be idempotent: the same completion notification should not create
duplicate downstream records.

## The output link has expired

PDF.co temporary output links are not permanent storage. The documented default
for `expiration` is 60 minutes, and the maximum duration depends on the
subscription plan.

If a downstream system needs the document later:

- Download or copy the output into the system's controlled storage before the link expires.
- Set a longer `expiration` value when the plan permits it.
- Use PDF.co built-in storage or another approved storage service for reusable files.

Increasing expiration is not a substitute for a retention policy. Decide how
long business documents should remain available and who can access them.

## The cURL output contains `\u0026`

The response may represent an ampersand in a URL as the Unicode escape
sequence `\u0026`. This is normal JSON encoding. A JSON parser decodes it to
`&`, and the URL remains valid.

Do not repair the value by string replacement before parsing the JSON. Parse
the response first, then use the decoded `url` value.

## The merge succeeds but the workflow still fails

Check the boundary between PDF.co and the next system:

1. Did the workflow check `error` and `status` before using `url`?
2. With `async` enabled, did it use `url` before Job Check or callback confirmed completion?
3. Did it download the temporary output before expiration?
4. Did it preserve the `pageCount`, `credits`, and request identifier in logs?
5. Did the next system receive a file, a URL, or base64 data as configured?

Keep the original request metadata and the PDF.co response together in an
auditable record. That makes intermittent failures distinguishable from
invalid input.

## Related pages

- [Merge PDF files](./merge-pdfs.md).
- [Merge PDF API reference](./merge-reference.md).
- [Understand how credits are charged](./credits.md).
