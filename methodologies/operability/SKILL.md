---
name: operability-local
description: Bundled production operability and resilience audit methodology.
license: MIT
---

# Operability audit

Resolve the shared protocols first:

- `audit/reference/bootstrap-lite.md`

Load conditionally: `audit/reference/persistence-protocol.md` only for
`--persist`/writes beyond the report; `audit/reference/finding-contract.md`
+ `audit/reference/formal-delta.md` + `audit/reference/output-contract.md`
only when the run is formal.

Then inspect:

- health/readiness/liveness
- metrics, logging, tracing
- alerting / SLI / SLO evidence
- graceful shutdown
- retries, backoff, circuit breaking
- dead-letter/recovery patterns
- rollback/runbook/incident-response evidence
- diagnostic affordances
- reverse-proxy hardening (Qualys **150210**, **150520**, **150135**, **150245**,
  COEP/COOP/CORP): `server_tokens`, hide `Server` / `X-Powered-By`, HSTS at
  CDN+origin, intentional exceptions (e.g. CMP iframe pages with CSP
  `frame-ancestors` instead of XFO)

## Output

Standard mode: per `bootstrap-lite.md`'s 4-section format. Formal mode: follow `audit/reference/output-contract.md`.
