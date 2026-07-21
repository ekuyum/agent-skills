---
name: full-system-health-audit
description: Weekly unattended security-first health audit of the whole Hermes install — exposure perimeter, memory, skills, platforms, sandbox, cron, performance, integrations, config hygiene, integrity. Load when asked to run a full system health audit, security audit, or system guardian check. Zero assumptions — every claim verified with live commands. Security and privacy first; minimal external surface; fix clear low-risk issues autonomously, escalate only genuine value trade-offs.
version: 1.0
---

# Full System Health Audit (System Guardian)

Highest-integrity, security-obsessed audit. Verify everything live; never assume. Produce a clear report; apply safe fixes; escalate only close trade-offs.

## ABSOLUTE RULES
1. Zero assumptions — every claim verified with live commands/reads/network checks.
2. Security & privacy first — external access / credential leakage / data exposure = P0, fix immediately if clear+low-risk.
3. No irreversible actions without extreme certainty — prefer reversible/staged/approval-gated.
4. Research → Plan → Double-check → Act.
5. Autonomous resolution preferred; escalate only when options are near-equal and human values required.
6. Minimal external surface — prefer air-gapped/restricted; NEVER open new network exposure.
7. Designed to run unattended via weekly cron while the user sleeps.

## SYSTEMS (strict priority order)
1. **Security & Exposure Perimeter** — no unauthorized listeners/public endpoints; gateway auth (allowlists, pairing, unauthorized_dm_behavior); secrets (.env perms 600, no plaintext leakage); sandbox isolation; dangerous-command approvals + blocklist + Tirith/SSRF; file-write safety (HERMES_WRITE_SAFE_ROOT); no public dashboard/proxy/tunnel; cross-session + cron path hardening; no excess PII to external LLMs.
2. **Memory** — MEMORY.md/USER.md char usage + density; correctness/freshness; no secrets/low-signal; external provider; write-approval + hygiene.
3. **Skills** — directory integrity, progressive disclosure, write-approval gates; dangerous/un-scanned community skills; /learn/health.
4. **Connections & Platforms** — active messaging platforms + auth configs; gateway isolation + profile separation.
5. **Terminal/Sandbox/Tool exec** — backend isolation strength; resource limits; env passthrough; credential mounting; code-exec mode + env scrubbing.
6. **Scheduling & Unattended Jobs** — cron safety, delivery targets, failure modes; this audit itself scheduled + isolated.
7. **Performance/Reliability/Learning** — latest stable version, latency, durable ledger, self-improvement settings, learning-loop health.
8. **External Integrations** — Obsidian/MCP/vault connections can't leak outward; isolation respected.
9. **Config & Secrets Hygiene** — config.yaml, .env, auth files, permissions.
10. **Overall Integrity** — logs for anomalies, state disk usage, backup readiness, update status.

## PROCESS (every finding)
Verify live → research docs → formulate reversible fix → double-check against state → (clear+low-risk: execute; close: document both options + escalate only that decision) → log the action.

## OUTPUT (strict)
1. **Executive Security Verdict** — Critical/Elevated/Acceptable/Excellent + one-sentence top finding.
2. **Full System Status Table** — System | Status (Healthy/Degraded/Critical) | Evidence | Action.
3. **Security & Privacy Deep Dive** — exposure, credential hygiene, isolation strength, residual risks + mitigations.
4. **Autonomous Fixes Applied** — exact before/after.
5. **Escalations** — only if needed, with pros/cons (keep empty when possible).
6. **Residual Recommendations** — low-priority, can wait.
7. **Audit Integrity Statement** — all claims verified live, least privilege preserved.

## CRON BEHAVIOR
Runs 03:00 local every Sunday, fresh isolated session, report to private channel/local file only, strictest safe approvals. Report ≥60 lines goes to Drive (HR#29) + a ≤3-line private summary.

Safety is not optional. Privacy is non-negotiable. Excellence compounds through disciplined, verified self-care.
