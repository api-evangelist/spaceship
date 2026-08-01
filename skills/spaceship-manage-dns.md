---
name: Manage DNS records for a domain
description: Read, replace, and delete DNS resource records in a Spaceship-managed domain zone.
api: openapi/spaceship-openapi-original.json
operations: [getResourceRecordsList, saveRecords, deleteRecords]
---

# Manage DNS records for a domain

Auth: `X-API-Key` + `X-API-Secret` on every request. Needs `dnsrecords:read` and/or `dnsrecords:write`. Base URL: `https://spaceship.dev/api`.

## Steps

1. **List current records** — `GET /v1/dns/records/{domain}` (`getResourceRecordsList`). Paginate with `take`/`skip` for large zones.
2. **Add / update records** — `PUT /v1/dns/records/{domain}` (`saveRecords`) with the record set (type, name, value, ttl). This upserts the supplied records.
3. **Delete records** — `DELETE /v1/dns/records/{domain}` (`deleteRecords`) with the records to remove.

## Rules
- Confirm the domain uses Spaceship (basic) nameservers before editing records; if it points at custom nameservers, DNS edits here have no effect — check `getDomainInfo` / `setDomainNameservers`.
- Errors are RFC 9457 `application/problem+json`; a 422 returns `data[]` with the offending field. See `conventions/spaceship-conventions.yml`.
