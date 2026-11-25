---
name: security-generic
description: Generic cybersecurity and infrastructure audit bundled inside the audit router.
---

# Cybersecurity And Infrastructure Auditor

Run a repo-grounded audit focused on exploitability, blast radius, and
production failure modes. Work only from evidence in the repository,
command output, and explicitly cited external sources for runtime/version
support status.

Resolve coverage and persistence first:

- follow `audit/reference/bootstrap-lite.md`

Load conditionally: follow `audit/reference/persistence-protocol.md` only
for `--persist`/writes beyond the report; follow
`audit/reference/finding-contract.md` + `audit/reference/formal-delta.md`
+ `audit/reference/output-contract.md` only when the run is formal.

## Scope

Always inspect:

- auth, session, cookie, OAuth, OTP, admin, webhook, and demo-entry flows
- Docker, compose, runtime images, reverse-proxy assumptions, env loading, secret handling
- schedulers, background jobs, cron-like tasks, queues, cache, DB, health/readiness behavior
- third-party connectors that move money or sensitive data
- dependency/runtime currency for the main runtime, main framework, auth stack, and up to 2 critical integrations

Tag statements as `Observed`, `Inferred`, or `Not verifiable from repo`.

## Workflow

1. Inventory the stack and trust boundaries.
2. Read runtime-defining files, not just README text.
3. Search for auth, secrets, cookies, CORS, webhook auth, demo switches, schedulers, external HTTP calls, and health checks.
4. Check whether production safeguards fail closed or fail open.
5. Identify the highest-impact findings first.
6. Verify runtime currency online using primary sources only.
7. Return a severity-ordered report with concrete file references and remediation steps.

## Recommended commands

```bash
rg --files .
rg -n "SECRET|TOKEN|PASSWORD|KEY|CORS|COOKIE|JWT|OTP|OAUTH|WEBHOOK|ALLOW_PUBLIC_DEMO|self_registration" .
rg -n "scheduler|BackgroundScheduler|cron|celery|rq|apscheduler|health|readiness|liveness" .
rg -n "httpx|requests|fetch\\(|axios|webhook|oauth|auth" .
rg -n "SessionMiddleware|CORSMiddleware|set_cookie|Authorization|Bearer|SameSite|Secure|HttpOnly" .
rg -n "innerHTML|dangerouslySetInnerHTML|document\\.write" .
```

## Qualys / WAS / DAST triage (no extra slug)

When the user imports a Qualys scan report, orchestrate **existing** commands —
do not invent a new audit slug. Static repo review does not replace Qualys;
live payload replay requires `pentest` phase 2 with explicit authorization.

| Qualys group / QID (typical) | Run slug(s) | Repo focus |
|-----------------------------|-------------|------------|
| Reflected chars / XSS (150084) | `api`, then `security` | Public query echo; JSON must not reflect raw input |
| DOM XSS (150938) | `security`, optional `pentest` (phase 2) | `innerHTML` sinks; cookie/URL taint to DOM |
| SQL injection | `security`, `api` | Parameterized SQL / ORM |
| Path manipulation (150622) | `pentest`, `operability` | Middleware/nginx; forbidden path segments |
| Cookie flags (150122, 150123, 150277, 150319) | `security`, `privacy` | `set_cookie`, `document.cookie` |
| Security headers (150135, 150245, 531928–531932) | `operability`, `cloud` | nginx, CDN, Fastify/Next headers |
| Server disclosure (150210, 150520) | `operability` | `server_tokens`, `X-Powered-By` |
| SRI (150261) | `supply-chain`, `security` | Third-party `<script integrity=` |
| Cache-Control (150249) | `api`, `operability` | Sensitive routes must not be public-cacheable |
| PII on forms (150375) | `privacy`, `security` | Public forms and logs |
| TLS / cert (38xxx) | `cloud` | Termination at CDN/ALB vs origin |

Suggested order after a Qualys drop: `api` → `security` → `pentest` (phase 1)
→ `operability` / `cloud` as needed → `verify` or external re-scan.

Report addendum: QID → fixed / accepted / N/A / needs re-scan; call out
findings only Qualys can see (CMP/GA cookies, CDN-only headers).

Before step 3, identify the target repo's actual auth library,
session/cookie mechanism, secret-loading approach, scheduler/queue
system, and sensitive third-party integrations. Adapt the grep patterns
to what the repo actually uses.

## Output

Standard mode: per `bootstrap-lite.md`'s 4-section format. Formal mode: follow `audit/reference/output-contract.md`.

Area-specific addendum:

- dependency/runtime currency summary with dates
- residual unknowns for runtime/deploy behavior

## Standard

- Prefer fewer high-confidence findings over many speculative ones.
- Distinguish code risk from deployment risk.
- Call out intentionally risky features protected only by configuration.
