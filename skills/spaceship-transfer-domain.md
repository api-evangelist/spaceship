---
name: Transfer a domain in
description: Initiate an inbound domain transfer to Spaceship and track it to completion.
api: openapi/spaceship-openapi-original.json
operations: [transferRequest, getTransferInfo, updateTransferLock, getAsyncOperationDetails]
---

# Transfer a domain in

Auth: `X-API-Key` + `X-API-Secret` on every request. Needs `domains:transfer` (and `contacts:write` if setting new contacts). Base URL: `https://spaceship.dev/api`.

## Steps

1. **Start the transfer** — `POST /v1/domains/{domain}/transfer` (`transferRequest`) with the authorization/EPP code from the losing registrar and the contact set. Long-running: expect **202** + `spaceship-async-operationid`.
2. **Track status** — `GET /v1/domains/{domain}/transfer` (`getTransferInfo`) for transfer state, and/or poll `GET /v1/async-operations/{operationId}` (`getAsyncOperationDetails`).
3. **Manage the lock** — once the transfer completes, `PUT /v1/domains/{domain}/transfer/lock` (`updateTransferLock`) to relock the domain against unauthorized transfers out.

## Rules
- The domain must be unlocked and have a valid auth code at the source registrar before `transferRequest`.
- To transfer a domain **out** of Spaceship instead, use `GET /v1/domains/{domain}/transfer/auth-code` (`getAuthCode`) to retrieve the EPP code.
- Errors are RFC 9457 `application/problem+json`; a 409 typically signals an in-flight or ineligible transfer.
