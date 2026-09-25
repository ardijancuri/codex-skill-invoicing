# UJP integration reference

This is a map for implementing against the **current** official specification, not a substitute for it. UJP has revised its API calls and JSON format during the pilot. Check the documents and live catalogs for each project, and record the date/version used.

## Source map

| Purpose | Official source |
| --- | --- |
| Project guidance and testing steps | [UJP guidance](https://www.ujp.gov.mk/-/javni_informacii/category/2074) |
| Current project documentation | [e-Faktura wiki](https://efakturawiki.ujp.gov.mk/) |
| API signing, submission, and field rules | [Public API PDF](https://efakturawiki.ujp.gov.mk/downloads/api-documentation-public8.pdf) |
| Example invoice JSON | [JSON examples](https://efakturawiki.ujp.gov.mk/downloads/primer_za_json_01.09.pdf) |
| Current test read/status/catalog operations and schemas | [Test OpenAPI](https://efakturatest.ujp.gov.mk/einvoice_api/v3/api-docs) |
| Taxpayer login, certificate registration, document UI | [Test platform](https://efakturatest.ujp.gov.mk/) |
| Test registration and privileges | [EUJP test registration](https://eujptest.ujp.gov.mk/ureg) |

The test OpenAPI served an OpenAPI 3.1 document with company, server-time, catalog, sales/purchase IDs, payload, status, PDF, and purchase accept/reject routes when last checked on 2026-09-26. The signed sales submission receiver is documented separately in the API PDF; do not assume the OpenAPI path list covers sending. Resolve any difference in favor of the current official documentation and observed server schema.

At that check, the test API exposed `/api/v1/server-time`, `/api/v1/companies/{tax_number}`, `/api/v1/document-statuses`, `/api/v1/doc-type-x-tax-indicator/list`, and catalogs for payment types and reject, correction, and void reasons. The `/api/v1/documents/` family exposed sales and purchase ID lists, payloads, current statuses, changes, and PDFs, plus purchase accept/reject. The tested signed sales receiver was `https://efakturatest.ujp.gov.mk/JSONReceiver/api/v1/sales-invoices/send`. These are discovery cues; verify paths, methods, schemas, and availability against the live spec before use.

## Connection and signing

- Configure taxpayer EDB, EUJP ID, test endpoint, and an approved signing certificate separately from production configuration. Validate the certificate's validity, private-key *availability* to the running process, and UJP registration/privileges without exporting its private key.
- Current test requests use signed JWS payloads (RS256 in the tested flow), a UJP request timestamp, and identity/certificate headers such as `X-EDB`, `X-EUJP-ID`, and `X-SERIAL-NUMBER`; sales submission also identifies its document type. Confirm exact fields, canonical JSON, serial-number representation, clock tolerance, and endpoint paths from the current specification before coding. Time zone and timestamp formatting are interoperability details.
- Check server time, company registry identity/address/VAT registration, available document types and tax indicators, payment types, correction/storno/rejection reasons, and signed list access before the first write. Check PDF and payload privileges with a real sandbox document.

## Document flow and data boundaries

- Build the UJP document from an immutable snapshot of a reviewed local version. Validate parties, item codes/descriptions/units, dates, accounting number and UJP document identity, tax group, totals, rounding, currency, payment description, and references against current schemas and catalogs. UJP totals can differ from local presentation rounding; display both before approval and block unexplained differences.
- A type 100 invoice with 18% VAT (`DDV-A`) and a non-VAT taxpayer example using `DDV-G` at 0% were exercised in a prior sandbox project. They are examples, **not** universal defaults. A company whose UJP registry shows no VAT registration must not be treated as VAT registered because a certificate contains a VAT-like label.
- Store each submission attempt and confirmed version, UJP EUID, QR/verification link, exact reviewed payload, response/error, UJP status history, reference to the prior EUID for storno/correction, actor, and timestamps. Keep this external status separate from local paid/unpaid state. Block ordinary mutation of submitted invoice facts; use the documented UJP correction or storno operation.
- On timeout, transport failure, missing EUID, or ambiguous server response, mark the operation uncertain and query UJP by a stable document identity before another send. A definite validation error may be corrected and re-prepared. Bound retries; do not turn one server-specific transient error into a universal retry rule.
- For incoming documents, show all UJP records, but import only types and tax treatments supported by the host app. Refresh current UJP status before accepting/rejecting or importing, map supplier and lines explicitly, compare local totals, and enforce EUID uniqueness and transactional local effects. Apply stock, payment, and balance adjustments only where the host app's domain model requires them, once per confirmed operation.

## Troubleshooting signals

- A successful UJP EUID, status lookup, PDF, or public verification page proves an API-side document exists. It does not prove the signed-in user's portal report lists it. Verify portal account/taxpayer, role, date range, filter, search action, and exact EUID separately; record a persistent mismatch as an unresolved UJP portal issue.
- During one sandbox integration, UJP rejected overly long item/document identifiers and a payment description that did not match its catalog. Check limits and catalog text from the current specification instead of copying that project's values. One test receiver also intermittently returned a validation-looking response for a payload later accepted; reconcile by document identity before treating a resend as safe.
