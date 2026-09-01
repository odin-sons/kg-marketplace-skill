# kg-marketplace-skill

[![Lint skill](https://github.com/odin-sons/kg-marketplace-skill/actions/workflows/lint.yml/badge.svg)](https://github.com/odin-sons/kg-marketplace-skill/actions/workflows/lint.yml)
[![Agent security scan](https://github.com/odin-sons/kg-marketplace-skill/actions/workflows/agent-scan.yml/badge.svg)](https://github.com/odin-sons/kg-marketplace-skill/actions/workflows/agent-scan.yml)
[![Link check](https://github.com/odin-sons/kg-marketplace-skill/actions/workflows/link-check.yml/badge.svg)](https://github.com/odin-sons/kg-marketplace-skill/actions/workflows/link-check.yml)

An [Agent Skill](https://agentskills.io/specification) that helps AI agents author and validate config files for the Valheim mod **Marketplace and Server NPCs (Revamped)** — quests, NPCs, dialogues, traders, factions, and every other `Configs/` system the mod ships.

General-purpose by design: it covers the mod's config *format and systems*, not any one server's balance choices. A different server's quest rewards or shop prices are none of this skill's business.

## What it does

Point an agent at this skill when a server admin needs to:

- Write or edit a `.cfg` file under any mod's `Configs/` subfolder
- Debug why a config entry silently isn't working in-game
- Understand what a specific field, condition, command, or reward type does
- Set up a new system on a server for the first time

It doesn't vendor a copy of the docs — [`SKILL.md`](SKILL.md) links out to the canonical reference at [kg-marketplace.pages.dev](https://kg-marketplace.pages.dev/) (also fetchable in one shot as [`llms-full.txt`](https://kg-marketplace.pages.dev/llms-full.txt)), so it stays correct as the mod changes instead of drifting stale.

`SKILL.md` itself is 79 lines / ~2.4k tokens — well inside the spec's recommended budget (<500 lines, <5k tokens) for what stays loaded whenever the skill triggers.

**Does it actually help?** [Evals](evals/) compare an agent with this skill against one without, on realistic tasks, graded against fixed assertions: **100% (13/13) with the skill vs. 23% (3/13) without.**

## Install

**Claude Code** — clone into your skills directory:

```bash
# Personal (all your projects)
git clone https://github.com/odin-sons/kg-marketplace-skill.git ~/.claude/skills/kg-marketplace-skill

# Project-local (this repo only)
git clone https://github.com/odin-sons/kg-marketplace-skill.git .claude/skills/kg-marketplace-skill
```

**Other agents** — this repo is a plain [Agent Skills](https://agentskills.io/specification)-compliant `SKILL.md`; consult your agent's own docs for where it expects skill directories. See the spec's [client showcase](https://agentskills.io/clients) for which products support the format.

## Development

[`SKILL.md`](SKILL.md) is the whole skill. See [CONTRIBUTING.md](CONTRIBUTING.md) for what CI checks on every push/PR (spec compliance, security, link rot), how to run those checks locally, and — the thing none of them can check — how `SKILL.md`'s factual claims get verified and reported when wrong.
