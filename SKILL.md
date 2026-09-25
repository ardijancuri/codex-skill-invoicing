---
name: e-faktura
description: Plan, implement, or test North Macedonia UJP e-Faktura integration in an existing app or software system. Use for UJP API, signed invoice submission, incoming documents, storno/correction, or sandbox readiness; do not apply to unrelated countries' e-invoicing.
---

# UJP e-Faktura

Adapt the integration to the host app. Do not carry over another project's architecture, tax choices, inventory behavior, or sample taxpayer data as defaults.

## Workflow

1. Inspect the app before asking questions: invoice and party models, tax and rounding, payments, inventory if any, permissions, deployment OS, secrets, migrations, and existing external integrations. Read [intake-and-plan.md](references/intake-and-plan.md); ask only consequential questions the app cannot answer. Ask in small groups, and resolve missing answers before fixing the implementation design.
2. Read [ujp-integration.md](references/ujp-integration.md). Open the official UJP links there and check the current API and catalog responses. Before coding, complete its contract checklist for every selected operation: method/path, headers and JWS, request/response fields, validation, status and error handling. Record the source URL and retrieval date/version in the project plan. UJP's current official material takes precedence over this skill and past implementations.
3. Produce a decision-complete, app-specific plan covering data flow, UI or API review steps, signing, external and local status, storage and audit, errors and reconciliation, permissions, migrations, testing, and rollout. Clearly mark features that do not apply to the app. If the request is planning only, stop at the plan. If implementation is authorized, carry it through and verify it.
4. Use [sandbox-acceptance.md](references/sandbox-acceptance.md) for tests and evidence. Keep test and production endpoints separate. Run live sandbox mutations only for the taxpayer, counterparties, and scenarios authorized for that project. Record each EUID, returned status/error, and local effect. Treat a self-addressed invoice as a smoke test, not proof of cross-taxpayer exchange.

## Invariants

- Never put certificate private keys, passwords, tokens, or real taxpayer credentials in this skill, source code, logs, or commits. Prefer signing with a protected key store, HSM, or equivalent available in the deployment environment; ask where the authorized certificate lives, not for the key itself.
- Derive invoice eligibility and VAT treatment from the taxpayer's verified registration and current UJP document-type/tax-indicator catalogs. Do not assume every business uses 18% VAT, MKD-only invoices, inventory, or the same invoice model.
- Separate UJP document status from local payment, fulfillment, and accounting status. Persist submitted versions and references; prevent ordinary edits that would contradict a submitted document. Reconcile uncertain submissions with UJP before any retry that could duplicate them.
- Verify API acceptance and signed-in portal visibility separately. An EUID in the API list or a public verification page does not prove that a particular user can see it in the portal report.

## Official sources

- [UJP public guidance](https://www.ujp.gov.mk/-/javni_informacii/category/2074)
- [UJP e-Faktura wiki](https://efakturawiki.ujp.gov.mk/)
- [Public API PDF](https://efakturawiki.ujp.gov.mk/downloads/api-documentation-public8.pdf)
- [JSON examples](https://efakturawiki.ujp.gov.mk/downloads/primer_za_json_01.09.pdf)
- [Test OpenAPI specification](https://efakturatest.ujp.gov.mk/einvoice_api/v3/api-docs)
- [UJP test platform](https://efakturatest.ujp.gov.mk/)
- [Test registration and privileges](https://eujptest.ujp.gov.mk/ureg)
