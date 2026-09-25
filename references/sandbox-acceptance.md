# Sandbox acceptance and evidence

Choose test cases that match the app's business scope and the user's authorization. Use clearly labeled sandbox records and amounts. Never present an intentionally rejected test invoice as a failed send; record who rejected it, when, why, and its UJP reason code.

## Checks before mutation

- Confirm selected taxpayer, EUJP ID, certificate, registry identity and VAT status, privileges, server time, active UJP catalogs, configured environment, authorized test counterparties, and operator roles.
- Confirm the intended invoice type/tax treatment is eligible. Review the exact seller, buyer, lines, VAT groups, totals, currency, references, and document identity before sending. Keep production submission disabled until its separate rollout criteria are met.

## Evidence matrix

| Scenario | Evidence to retain |
| --- | --- |
| Payload and signing | Rounding, line and document identity limits, tax group, JWS verification with public certificate, timestamp/serial compatibility; no secret in logs |
| Outgoing send | Reviewed payload, actor, UJP response and EUID, QR/verification link, status read, UJP PDF and payload read |
| Portal visibility | Signed-in correct taxpayer and role, date/filter/search values, exact EUID result; document an API-versus-portal mismatch rather than assuming visibility |
| Incoming | ID list and payload/PDF, current status, reviewed accept and reject cases with reasons, supported local import with supplier/item mapping and total check |
| Duplicate and uncertainty | Repeated EUID import blocked; ambiguous send reconciled before retry; race or repeated callbacks do not double-apply local effects |
| Storno/correction | UJP reference and reason, preserved prior version, new EUID/status, one-time local accounting and optional stock difference; retain payment history and flag overpayment if the app tracks payments |
| Permissions and failure | Unauthorized role blocked; unavailable certificate/privilege and definite UJP validation errors leave recoverable, auditable states |

Cross-taxpayer exchange requires a second registered sandbox taxpayer. A self-addressed invoice can prove transport, signing, lookup, and some status flows but is not sufficient evidence of real counterparty delivery or portal visibility. A tax treatment unavailable to the selected taxpayer must remain untested live and be marked as such even if unit tests pass.

Finish with a concise test record: specification version/date, taxpayer and environment (avoid secrets), authorized test scenarios, EUIDs, UJP statuses/errors, local effects, portal observations, unresolved privileges or discrepancies, and whether production remains gated. Stop further live submissions when the same unexplained failure repeats or authorization for another scenario is absent; preserve the result for investigation.
