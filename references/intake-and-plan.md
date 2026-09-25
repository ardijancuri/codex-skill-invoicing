# App discovery, questions, and plan

Use this guide to plan for the app at hand. Inspect code, configuration, database, and existing workflows first. Do not ask the user to repeat facts already visible there. Ask only the relevant unanswered questions, preferably with a recommended option when there is a real tradeoff. Do not request passwords or a certificate private key in chat.

## Discovery and question bank

### Business and UJP identity

- Which legal taxpayer will issue and receive documents? Obtain or locate its EDB and EUJP ID through the app's secure settings. Verify the UJP company registry and structured address. Is it VAT registered? Confirm rather than infer registration from a certificate label.
- Which UJP document types, tax treatments, currencies, domestic or foreign buyers, and invoice directions are in scope? Use current UJP catalogs to decide eligibility. Ask about exemptions, reverse charge, advance invoices, and other cases only if the app actually handles them.
- Which test environment privileges and certificate registrations already exist? Which second registered taxpayer can participate in a cross-taxpayer test? Ask for the counterpart only when that test is in scope.

### Host app and operators

- Where are invoice number, date, parties, lines, tax, totals, payment and accounting state stored? What is the source of truth when UJP and the app disagree? Are invoices already immutable after posting?
- Does an invoice affect stock, services, orders, deposits, or balances? Which event currently applies those changes? Ask how storno and correction should affect them only when those features exist.
- Which roles may prepare, confirm, send, accept/reject, import, storno, correct, view PDFs, and configure certificates? Is a second reviewer or approval step required?
- What language, UI surface, and audit expectations apply? Is a headless API integration sufficient, or do operators need review screens and an inbox?

### Deployment and testing

- Which operating systems and hosts run the signer? Is the authorized certificate in an OS store, HSM, managed key service, secure file, or smart card? Who controls renewal? Can the application account access its signing operation?
- What environments, secrets system, database migration process, job runner, and monitoring already exist? How are external requests timed out and retried?
- What sandbox scenarios and test values are authorized? Who can verify the signed-in UJP portal and supply evidence? When, if ever, may production sending be enabled?

## Plan deliverable

Make the plan decision-complete for the discovered app. Include:

1. **Business scope and eligibility:** taxpayer registration, supported document/tax cases, unsupported cases, roles, and the UJP documentation version checked.
2. **Integration design:** signing boundary and certificate access; UJP client and environment configuration; company/catalog diagnostics; requested outgoing and incoming workflows; status/PDF/QR; storno/correction when requested.
3. **App data flow:** mappings between UJP and local parties/lines/totals; immutable operation records and EUID links; payment/accounting, inventory, or other domain effects only if present; exact transaction and duplicate boundaries.
4. **Failure behavior:** validation failures, definite UJP rejection, uncertain response, lookup/reconciliation before retry, audit history, operator recovery, and privilege or certificate errors.
5. **Acceptance and rollout:** unit/integration tests, authorized sandbox cases, UJP API evidence, signed-in portal evidence, unresolved cases, and production gate.

State necessary assumptions and blockers. Do not freeze a tax rate, reason code, UJP status, or endpoint solely because it worked in a previous app. If a user authorizes implementation in the same request, the plan guides the work without an additional generic approval step.
