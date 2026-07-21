---
name: loop-engineering
description: Design systems that prompt and orchestrate agents on a cadence — instead of prompting them manually. Load when building/reviewing any recurring agent loop (cron job, supervisor, watcher, sweeper, triage loop), when a loop misbehaves (floods, stalls, auto-merges wrongly), or when deciding whether a task should be a loop at all. Captures the loop-design checklist, maker/checker split, state discipline, human-handoff gates, and the loop-audit/cost/gate CLIs.
version: 1.0
---

# Loop Engineering

**Core principle (Steinberger/Cherny/Osmani):** don't prompt the agent — design the loop that prompts the agent. The leverage moved from crafting prompts to orchestrating agents over time. Source: `github.com/cobusgreyling/loop-engineering`.

## When a task becomes a loop (and when it doesn't)
A task is a loop candidate if it recurs on a cadence, has a verifiable "done", and a human gate exists for the risky part. If any of those is missing, it's a one-off — do it, don't loop it.

## Anatomy of a loop
```
Schedule → Triage skill → Read/Write STATE → isolated worktree →
Implementer sub-agent → Verifier sub-agent (tests in worktree) →
MCP/Git/Tickets → Human Gate → Commit/PR or Escalate → back to Schedule
```

## The design checklist (run before enabling ANY loop in production)
1. **Purpose & scope** — one-sentence goal + explicit non-goals + watched scope + phased rollout (report-only first).
2. **Scheduling** — cadence matches urgency; durable across restarts; off-hours behavior defined; self-cleanup when watchlist empties.
3. **Skills** — triage skill has a tight output format; descriptions are boring+specific (good auto-triggering).
4. **Maker/Checker split** — implementer and verifier are SEPARATE; implementer can NEVER mark its own work done; verifier runs tests in an isolated worktree before approving. (This is your worker/supervisor + eval-gate doctrine — enforce it mechanically.)
5. **State/memory** — state file schema documented; loop reads prior state at start, writes outcomes+timestamps; prunes resolved items every run; human overrides recorded.
6. **Human handoff** — escalation triggers explicit (max attempts, risk paths, ambiguity); denylist = auth/payments/secrets/infra; notify human ONLY when action required.
7. **Connectors** — scope MCP/tools to read+comment until the loop is trusted.

## Autonomy rollout (never skip a level)
- **L1 report-only** → **L2 assisted fixes** → **L3 unattended**. A loop missing verification is not ready for L2/L3.

## Mechanical gates (the parts worth stealing)
- **loop-gate** — evaluates proposed changes against a machine-readable `gate.yaml` (denylist paths + auto-merge allowlist); exits 0 (allow) or 2 (escalate). No run-history — that's the circuit breaker's job. Chain it: `loop-context --check ... || exit 2; loop-gate check ... || exit 2`.
- **Token budget** — every loop has a hard token cap (`loop-budget`); estimate with `loop-cost` BEFORE enabling.
- **Kill switch** — a single label/flag in STATE that pauses all loops. Non-negotiable.
- **Worktree isolation** — any unattended code change runs in an isolated git worktree per attempt; discarded on verifier REJECT.

## The CLIs (npm/npx)
- `npx @cobusgreyling/loop-audit . --suggest` — score a loop's readiness (constraints + governance)
- `npx @cobusgreyling/loop-cost --pattern <p> --level <L>` — token spend estimate
- `npx @cobusgreyling/loop-gate check --action auto-merge --paths <files>` — mechanical gate
- `npx @cobusgreyling/loop-init . --pattern <p> --tool <agent>` — scaffold a starter

## Map to this system (Hermes/cron/supervisor)
Your existing doctrine already implements most of this (worker/supervisor split, approval gates, CANON, hard rules, dedup choke point). Where loop-engineering ADDS value:
- **loop-gate as a mechanical pre-send/pre-merge gate** — complements the pre_send_checklist (HR#43) with a machine-enforced denylist.
- **loop-audit scoring** — a weekly readiness score for your 80 crons (which are L1 vs L2 vs should-be-killed).
- **loop-cost** — before enabling any paused cron, estimate its token spend against the budget.

## Pitfalls
- Never let the implementer self-verify (the #1 loop failure). Maker ≠ checker, always.
- A loop without a verifiable "done" stalls or floods — write the success criterion BEFORE the loop, not after.
- Auto-merge only trivial, allowlisted, verifier-passed changes. Everything else = human gate.
- Kill switch first, features second. If you can't pause it in one step, don't enable it.
