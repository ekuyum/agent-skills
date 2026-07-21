# agent-skills — Shared Agent Skill Registry

Single source of truth for skills used across **Hermes**, **Claude Code**, and **Cowork** — all following the open [Agent Skills spec](https://agentskills.io) (SKILL.md + YAML frontmatter).

## Layout

```
<skill-name>/SKILL.md
```

One directory per skill. Each has a `SKILL.md` with `name` + `description` frontmatter.

## Install a skill

**Hermes:**
```bash
hermes skills install ekuyum/agent-skills/<skill-name>
```

**Claude Code / Cowork:**
```bash
npx skills add ekuyum/agent-skills --skill <skill-name> -g -a claude-code -y
```

## Update all skills

**Hermes:** `hermes skills update`
**Claude Code:** `npx skills update -g -y`

## Add a new skill

1. Create `<skill-name>/SKILL.md` (frontmatter: `name`, `description`, then the workflow).
2. Commit + push.
3. Both agents pull it via their native install/update.

Private repo — access via the git credential already on the VPS and Efe's Mac.
