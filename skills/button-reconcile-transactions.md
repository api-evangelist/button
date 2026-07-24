---
name: Reconcile Button affiliate transactions
description: List billing accounts and page through all affiliate commission transactions across currencies.
api: openapi/button-billing-api-openapi.json
operations: [list-accounts, list-transactions, list-transactions-all]
---

# Reconcile Button affiliate transactions

Use this skill to reconcile affiliate earnings: enumerate billing accounts, then retrieve
commission transactions and follow cursor pagination to the end.

## Authentication
HTTP basic auth: `curl -u YOUR_API_KEY: https://api.usebutton.com/v1/affiliation/...`.

## Steps
1. **List accounts** — call `list-accounts` (GET `/accounts`). Your organization has one billing
   account per supported currency; capture each `id` (e.g. `acc-xxx`) and `currency`.
2. **Retrieve transactions** — either:
   - `list-transactions-all` (GET `/transactions`) for every account across all currencies, or
   - `list-transactions` (GET `/accounts/{account_id}/transactions`) for a single-currency account.
3. **Paginate** — follow the cursor in `meta.next`, passing it as `cursor`, until `meta.next` is
   `null`. Do not assume a fixed page count.

## Conventions and errors
- Response envelope: `{ meta: { status, next, previous }, objects: [ ... ] }`.
- Transactions carry `amount`, `currency`, `account`, and `line_items[]` (each with `identifier`,
  `total`, `amount`, `quantity`). See `data-model/button-data-model.yml`.
- `401` = invalid API key. See `errors/button-problem-types.yml` and `conventions/button-conventions.yml`.
