# kg-marketplace-skill

An [Agent Skill](https://agentskills.io/specification) that helps AI agents author and validate config files for the Valheim mod **Marketplace and Server NPCs (Revamped)** — quests, NPCs, dialogues, traders, factions, and every other `Configs/` system the mod ships.

General-purpose by design: it covers the mod's config *format and systems*, not any one server's balance choices. A different server's quest rewards or shop prices are none of this skill's business.

## What it does

Point an agent at this skill when a server admin needs to:

- Write or edit a `.cfg` file under any mod's `Configs/` subfolder
- Debug why a config entry silently isn't working in-game
- Understand what a specific field, condition, command, or reward type does
- Set up a new system on a server for the first time

It doesn't vendor a copy of the docs — [`SKILL.md`](SKILL.md) links out to the canonical reference at [kg-marketplace.pages.dev](https://kg-marketplace.pages.dev/) (also fetchable in one shot as [`llms-full.txt`](https://kg-marketplace.pages.dev/llms-full.txt)), so it stays correct as the mod changes instead of drifting stale.

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

[`SKILL.md`](SKILL.md) is the whole skill. CI lints it on every push/PR with [`skillscheck`](https://github.com/agentskills/agentskills) in strict mode:

```bash
uvx skillscheck . --strict
```

On Windows, run it with `PYTHONIOENCODING=utf-8` set — `skillscheck`'s own info symbols crash under the default console codepage.
