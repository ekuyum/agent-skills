---
name: shared-skill-registry
description: The shared skill registry system — how Hermes, Claude Code, and Cowork install, update, and publish skills from the single source of truth (ekuyum/agent-skills GitHub repo). Load when adding a new shared skill, syncing skills across agents, onboarding a new machine/agent, or when asked "how do skills work across Hermes and Claude Code."
version: 1.0
---

# Shared Skill Registry

## What this is
One GitHub repo — **`ekuyum/agent-skills`** (public) — is the single source of truth for all custom agent skills. Hermes, Claude Code, and Cowork all install and update from it natively. No symlinks, no filesystem coupling. Works across VPS, Mac, and any future machine. Follows the open Agent Skills spec: `<skill-name>/SKILL.md` with YAML frontmatter (`name`, `description`).

## The registry
- Repo: `github.com/ekuyum/agent-skills` (public so both agents can pull without auth juggling)
- Layout: one directory per skill → `<skill-name>/SKILL.md`
- Current skills: `find-skills`, `shared-skill-registry` (this one)

## Install a skill (per agent)
**Hermes:**
```bash
hermes skills install ekuyum/agent-skills/<skill-name> --yes
# or direct URL:
hermes skills install https://raw.githubusercontent.com/ekuyum/agent-skills/main/<skill-name>/SKILL.md --name <skill-name> --yes
```

**Claude Code / Cowork:**
```bash
npx -y skills add ekuyum/agent-skills --skill <skill-name> -g -a claude-code -y
```

## Update all skills
- **Hermes:** `hermes skills update`
- **Claude Code:** `npx -y skills update -g -y`

## Add a NEW shared skill (write once, both get it)
1. Write `<skill-name>/SKILL.md`:
   - frontmatter: `name`, `description` (the description is the trigger — make it specific about WHEN to load)
   - body: the workflow — steps, pitfalls, verification
2. Commit + push to `ekuyum/agent-skills`.
3. Each agent pulls via its native install/update above.

## Onboard a new machine / agent
- **Hermes box:** `hermes skills install ekuyum/agent-skills/<skill>` for each skill wanted.
- **Mac Claude Code:** paste the onboarding prompt (below) or run the npx add per skill.

### Claude Code onboarding prompt (paste into the agent)
```
Set up the shared agent-skills registry so we use the same skill library as my
Hermes agent. Source of truth: public GitHub repo ekuyum/agent-skills
(Agent Skills spec: <skill-name>/SKILL.md).

1. Install the shared skills:
   npx -y skills add ekuyum/agent-skills --skill '*' -g -a claude-code -y

2. When I ask "is there a skill for X" or "how do I do X", FIRST check the
   shared registry (npx skills find <query> --owner ekuyum), then skills.sh.
   Prefer ekuyum/agent-skills skills — they're shared with my Hermes agent.

3. When you create a reusable skill, propose adding it to ekuyum/agent-skills
   as <skill-name>/SKILL.md so BOTH agents get it.

Confirm with `npx -y skills ls -g` and tell me when ready.
```

## Pitfalls
- **Private repo breaks raw-URL installs** — Hermes's raw-URL fetch doesn't carry git credentials (404). Keep the registry PUBLIC; skills are non-secret workflow patterns. Never put credentials/PII in a skill.
- **The `description` frontmatter is the trigger** — write it to say exactly WHEN the skill should load, or the agent won't surface it at the right moment.
- **Don't duplicate across agents' local dirs** — the registry is canonical; local copies are just installs. Edit in the repo, then update, not the other way.
- **`npx skills add` from a shortened link (t.co etc.)** — always resolve to the clean `github.com/owner/repo` URL first and vet the source (install count, org reputation) before installing.

## Verification checklist
- [ ] `hermes skills list` shows the skill enabled
- [ ] `npx -y skills ls -g` (Claude Code) shows the same skill
- [ ] Both pulled from `ekuyum/agent-skills` (same source)
- [ ] Edit in repo → `update` on both → both reflect the change
