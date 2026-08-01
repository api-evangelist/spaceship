---
name: List and sell a domain on SellerHub
description: List a domain for sale in Spaceship SellerHub, share a checkout link, and track the SafePay escrow transaction.
api: openapi/spaceship-openapi-original.json
operations: [createSellerHubDomain, getSellerHubDomain, createCheckoutLink, getSafePayTransaction]
---

# List and sell a domain on SellerHub

Auth: `X-API-Key` + `X-API-Secret` on every request. Needs `sellerhub:read` / `sellerhub:write`. Base URL: `https://spaceship.dev/api`.

## Steps

1. **List the domain for sale** — `POST /v1/sellerhub/domains` (`createSellerHubDomain`) with pricing/marketplace settings. Verify seller ownership first via `GET /v1/sellerhub/verification-records` (`getVerificationRecords`) if required.
2. **Confirm the listing** — `GET /v1/sellerhub/domains/{domain}` (`getSellerHubDomain`) to read back status and pricing; update with `PATCH /v1/sellerhub/domains/{domain}` (`updateSellerHubDomain`).
3. **Share a checkout link** — `POST /v1/sellerhub/checkout-links` (`createCheckoutLink`) to generate a shareable buy link for the domain.
4. **Track the sale** — when a buyer purchases, `GET /v1/sellerhub/safepay-transactions/{transactionId}` (`getSafePayTransaction`) tracks the SafePay escrow transaction; list all with `getSafePayTransactionList`. Sold reporting via `getSoldDomains`.

## Rules
- SafePay is the escrow mechanism protecting buyer and seller; do not release/expect funds outside the SafePay transaction state.
- Errors are RFC 9457 `application/problem+json`. See `errors/spaceship-problem-types.yml`.
