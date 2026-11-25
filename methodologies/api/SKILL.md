---
name: api-local
description: Bundled API and webhook audit methodology.
license: MIT
---

# API audit

Resolve the shared protocols first:

- `audit/reference/bootstrap-lite.md`

Load conditionally: `audit/reference/persistence-protocol.md` only for
`--persist`/writes beyond the report; `audit/reference/finding-contract.md`
+ `audit/reference/formal-delta.md` + `audit/reference/output-contract.md`
only when the run is formal.

Then inspect:

- endpoint inventory
- authentication and authorization
- object-level authorization
- rate limiting
- idempotency
- pagination
- validation and error contracts
- webhook signatures and replay protection
- timeout/retry and debug endpoints

### Qualys WAS (API-related QIDs)

If triaging a Qualys report, prioritize:

- **150084** — query/body must not be echoed in JSON/HTML; invalid params → 4xx
  without reflecting attacker input.
- **150249** — `Cache-Control` on public routes (stats, SEO) must match sensitivity.
- SQLi classes — parameterized queries on every user-controlled filter.

Cross-slug: DOM/cookies → `security`; path fuzz → `pentest` phase 1.

## Output

Standard mode: per `bootstrap-lite.md`'s 4-section format. Formal mode: follow `audit/reference/output-contract.md`.

Generate:

`endpoint x auth x authorization x validation x rate-limit x tests`
