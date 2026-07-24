---
name: Generate an attributable Button link
description: Fetch affiliate offers for a user and generate a fully attributable Button-wrapped link to a Retailer destination.
api: openapi/button-offers-api-openapi.json, openapi/button-links-api-openapi.json
operations: [offers, generate-a-link]
---

# Generate an attributable Button link

Use this skill to monetize outbound traffic: check the real-time affiliate offers available to
a user, then wrap the destination URL as an attributable Button link.

## Authentication
All Button APIs use HTTP basic auth with your API key as the username and an empty password:
`curl -u YOUR_API_KEY: ...`. Never place the key in a query string or client-side code.

## Steps
1. **Fetch offers** — call `offers` (POST `https://pubapi.usebutton.com/v1/offers`) with the
   Publisher user reference to retrieve the rates/offers available to that user.
2. **Generate the link** — call `generate-a-link` (POST `https://api.usebutton.com/v1/links`) with:
   - `url` (required): the target Retailer page URL.
   - `experience.btn_pub_user`: your stable user identifier — this value is echoed back in the
     transaction webhook so you can attribute (and reward) downstream Brand activity.
   - `experience.btn_pub_ref` (optional): a click/campaign reference, max 512 chars.
3. Redirect the user to the returned Button link. Downstream orders arrive as signed webhooks.

## Conventions and errors
- Responses use the `{ meta: { status }, object: ... }` envelope; success is `meta.status == "ok"`.
- `401` = missing/invalid API key; `403` = organization not allowlisted for the operation;
  `404` = destination/brand not found. See `errors/button-problem-types.yml`.
- There is no idempotency-key header; links are cheap to regenerate.
- Close the loop by consuming transaction webhooks (`asyncapi/button-webhooks-asyncapi.yml`).
