---
title: Merge PDF API reference
type: reference
audience: Operations managers and automation engineers
status: draft
---

# Merge PDF API reference

This is a reference for the PDF.co merge endpoint: request payload attributes, response fields, headers, and limits.

For a guided first request, read [Merge PDF files](./merge-pdfs.md). For credit fields, read [Understand how credits are charged](./credits.md).

## Endpoint

| | |
|---|---|
| Method | `POST` |
| URL | `https://api.pdf.co/v1/pdf/merge` |
| Content-Type | `application/json` |
| Authentication | `x-api-key` header |

Attribute names are case-sensitive. Pass attributes in the JSON body, not as query strings.

## Request attributes

| Attribute | Type | Required | Default value | Description |
|---|---|---|---|---|
| `url` | string | Yes | — | Source file URL(s). Multiple URLs: comma-separated string, not a JSON array. Order determines merge order. |
| `async` | boolean | No | `false` | If set to `true`, will do background processing and response includes `jobId`. Use [Background Job Check](https://developer.pdf.co/api/job-check) or `callback` to check on completion. |
| `callback` | string | No | — | Webhook URL for async completion. Only when `async` is `true`. |
| `name` | string | No | — | Output file name. |
| `expiration` | integer | No | `60` | Output link lifetime in minutes. Maximum value depends on subscription plan. |
| `httpusername` | string | No | — | HTTP Basic Auth username for protected source URLs. |
| `httppassword` | string | No | — | HTTP Basic Auth password for protected source URLs. |
| `profiles` | object | No | — | Advanced options. Profile tables are on this page. For the full list, read [Profiles](https://developer.pdf.co/api/profiles). |

Profile keys are nested inside the `profiles` object in the request body.

### Output format

| Profile key | Type | Default | Description |
|---|---|---|---|
| `outputDataFormat` | string | — | Set to `base64` to return output as base64 instead of a URL. |

### PDF forms

| Profile key | Type | Default | Description |
|---|---|---|---|
| `RenameMatchingFieldsDuringMerge` | boolean | `true` | Rename duplicate form field names when merging PDFs with forms. Set to `false` to retain original field names. |

### Bookmarks

| Profile key | Type | Default | Description |
|---|---|---|---|
| `GenerateBookmarks` | boolean | `false` | Add bookmarks to the merged document, one per source file, in merge order. |
| `BookmarkTitles` | array of strings | — | Custom bookmark names when `GenerateBookmarks` is `true`. One title per merged document, in merge order. |

### Document metadata

| Profile key | Type | Default | Description |
|---|---|---|---|
| `MergedDocumentTitle` | string | Title of the first document | Custom title for the merged PDF. Overrides the first document's title. |

### Zip filters

Use when a source URL points to a ZIP archive. Both filters support `*` and `?` wildcards.

| Profile key | Type | Default | Description |
|---|---|---|---|
| `zipIncludeFilter` | string | — | Include only matching files from the ZIP (for example, `*.pdf,*.xls*`). |
| `zipExcludeFilter` | string | — | Exclude matching files from the ZIP (for example, `*.doc*,*.xls*`). |

### User-controlled encryption

For encryption and decryption behavior, read [User-Controlled Encryption](https://developer.pdf.co/knowledgebase/user-controlled-encryption).

| Profile key | Type | Default | Description |
|---|---|---|---|
| `DataEncryptionAlgorithm` | string | — | Encryption algorithm: `AES128`, `AES192`, or `AES256`. |
| `DataEncryptionKey` | string | — | Encryption key. |
| `DataEncryptionIV` | string | — | Encryption initialization vector. |
| `DataDecryptionAlgorithm` | string | — | Decryption algorithm: `AES128`, `AES192`, or `AES256`. |
| `DataDecryptionKey` | string | — | Decryption key. |
| `DataDecryptionIV` | string | — | Decryption initialization vector. |

For the complete attribute list and code samples, read the [Merge PDF API reference](https://developer.pdf.co/api/merge/pdf).

## Response fields

Fields returned vary depending on whether the request is synchronous or asynchronous, and on response timing. Not every field appears in every response.

### Synchronous response (`async: false`)

| Field | Type | Description |
|---|---|---|
| `url` | string | Temporary URL to the merged PDF in S3. |
| `outputLinkValidTill` | string | Timestamp when the output link expires. |
| `name` | string | Output file name. |
| `pageCount` | integer | Page count in the merged document. |
| `error` | boolean | `false` = success. |
| `status` | string | Request status code (for example, `200`). Documented in [Response codes](https://developer.pdf.co/api/response-codes). |
| `credits` | integer | Credits consumed. |
| `remainingCredits` | integer | Account balance after the request. |
| `duration` | integer | Processing time in milliseconds. |

### Asynchronous response (`async: true`)

When `async` is `true`, PDF.co queues the merge in the background. The response includes a `jobId`. It may also include a `url` and other output fields. The output URL becomes available when the job completes, and you can use the [Background Job Check](https://developer.pdf.co/api/job-check) endpoint or the `callback` attribute to confirm completion before you download the file or pass it to the next workflow step.

| Field | Type | Description |
|---|---|---|
| `jobId` | string | Background job identifier. |
| `url` | string | Output URL. May appear in the async response. Use after the job completes. |
| `error` | boolean | `false` = request accepted or succeeded. |
| `status` | string | Request status code. Documented in [Response codes](https://developer.pdf.co/api/response-codes). |
| `outputLinkValidTill` | string | Timestamp when the output link expires. Present when `url` is returned. |
| `name` | string | Output file name. |
| `pageCount` | integer | Page count in the merged document. |
| `credits` | integer | Credits consumed. |
| `remainingCredits` | integer | Account balance after the request. |
| `duration` | integer | Processing time in milliseconds. |

## Limits and defaults

| Limit | Value |
|---|---|
| Combined input size | 2 GB maximum across all source URLs. |
| Default `async` | `false` |
| Default `expiration` | 60 minutes |
| Output link storage | [PDF.co Temporary Files Storage](https://developer.pdf.co/api/file-upload/overview) |

## Example request

This example merges two PDF files with a synchronous response.

```json
{
  "url": "https://pdfco-test-files.s3.us-west-2.amazonaws.com/pdf-merge/sample1.pdf,https://pdfco-test-files.s3.us-west-2.amazonaws.com/pdf-merge/sample2.pdf",
  "async": false
}
```

### Example response

```json
{
  "url": "https://pdf-temp-files.s3.amazonaws.com/3ec287356c0b4e02b5231354f94086f2/result.pdf",
  "outputLinkValidTill": "2026-08-15T12:00:00Z",
  "error": false,
  "status": "200",
  "name": "result.pdf",
  "pageCount": 4,
  "credits": 8,
  "remainingCredits": 98465,
  "duration": 1830
}
```

Field presence and values are illustrative. Always confirm against the [API Tester](https://developer.pdf.co/api-tester/merge/pdf).

## Related pages

- [Merge PDF files](./merge-pdfs.md).
- [Understand how credits are charged](./credits.md).
- [Fix errors in file merge actions](./troubleshooting.md).

