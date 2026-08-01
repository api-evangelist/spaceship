---
name: Register a domain
description: Check availability and register a domain on Spaceship, using an existing WHOIS contact, then confirm via the async operation.
api: openapi/spaceship-openapi-original.json
operations: [checkSingleDomainAvailability, saveDetails, domainCreate, getAsyncOperationDetails]
---

# Register a domain

Auth: send `X-API-Key` and `X-API-Secret` on every request (both required). This flow needs the `domains:write` and `contacts:write` scopes. Base URL: `https://spaceship.dev/api`.

## Steps

1. **Check availability** — `GET /v1/domains/{domain}/available` (`checkSingleDomainAvailability`). Confirm the domain is registrable and note the price/premium status. For bulk checks use `POST /v1/domains/available` (`checkDomainsAvailability`).
2. **Ensure a contact exists** — `PUT /v1/contacts` (`saveDetails`) to create/update the registrant/admin/tech/billing WHOIS contact. Keep the returned contact id.
3. **Create the registration** — `POST /v1/domains/{domain}` (`domainCreate`) with `years`, `autoRenew`, `privacyProtection`, and the `contacts` map (registrant/admin/tech/billing → contact id). This is long-running: expect **202 Accepted** with a `spaceship-async-operationid` response header.
4. **Poll for completion** — `GET /v1/async-operations/{operationId}` (`getAsyncOperationDetails`) until `status` is `success` (or `failed`). States are `pending | success | failed`.

## Rules
- Errors are `application/problem+json` (RFC 9457); read `detail`, and for 422 the `data[]` per-field list. See `errors/spaceship-problem-types.yml`.
- There is no Idempotency-Key; do not blindly retry `domainCreate` — instead resolve the async operation and check domain state before retrying.
- A 403 means the key lacks the scope; a 409 means a conflicting state (e.g. already registered).
