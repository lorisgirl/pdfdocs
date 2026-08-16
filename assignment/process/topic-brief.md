---
title: Topic brief — PDF.co merge API
type: process
audience: Assignment reviewers
status: draft
---

# Topic brief: PDF.co merge API

Assignment component 1. Describes what this documentation sample covers and why it exists.

## What this documentation covers

**Topic:** PDF.co `POST /v1/pdf/merge`, which allows a user to combine multiple PDF source files into one output PDF.

**Audience:** Operations managers and automation engineers who integrate PDF.co actions into business workflows (mainly using Zapier, Make, custom scripts, or internal tools).

## Problem this documentation solves

This set of documents provides:

* A structured path from first successful request through operational monitoring and failure diagnosis.
* Solutions for issues with failing requests.
* A solid reference for endpoint attributes in both requests and responses.
* Better awareness of how credits are used with this particular endpoint.


## What the reader can do after reading

1. Send a valid merge request with `curl` and read the response.
2. Choose synchronous or asynchronous processing for their job size.
3. Look up request and response fields without reading the full official reference.
4. Estimate and monitor credit usage using response fields and live pricing.
5. Diagnose common failures before they interrupt a production workflow.

## Prerequisites

- A PDF.co account and API key.
- Basic REST and `curl` familiarity.
- Source PDF files available at URLs PDF.co can reach over HTTPS.


## Reader-facing documentation

User documentation lives in [`../pdfco-merge/`](../pdfco-merge/). Start at [Merge PDF API](../pdfco-merge/README.md).
