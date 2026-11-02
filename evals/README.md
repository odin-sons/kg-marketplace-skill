# Evals

`skillscheck`, `snyk-agent-scan`, and `lychee` (see [CONTRIBUTING.md](../CONTRIBUTING.md)) check the skill's form, safety, and links. None of them check whether it actually helps — that's what this measures.

## Method

Three realistic tasks ([`evals.json`](evals.json)), each run twice: once by an agent with this skill loaded, once by a plain agent with no skill and no mod documentation (same model, same prompt). Each output was graded against a fixed set of objective, skill-specific assertions — e.g. "uses a real quest type from the mod's actual 10 types," not "sounds helpful."

## Results (2026-09-01)

| Eval | With skill | Without skill |
|---|---|---|
| Add a trader NPC that also gives a quest | 5/5 | 1/5 |
| Debug a non-working `HasGuildWithName` condition | 4/4 | 1/4 |
| Build a 3-quest chain with a hidden final boss | 4/4 | 1/4 |
| **Total** | **13/13 (100%)** | **3/13 (23%)** |

The one assertion each baseline run did pass was usually "didn't hallucinate a specific fake API" — the model correctly hedged rather than inventing false confidence. Everything specific to this mod's actual format, the baseline got wrong: it wrote plain-JSON config for a mod that uses plain-text `.cfg` files, named quest-chain fields that don't exist, and once confidently recommended a fix (stripping whitespace around commas) that the mod's own docs say is unnecessary — spaces there are already stripped automatically.

The skill isn't free: with-skill runs took ~2.3x longer on average (172s vs 73s, driven by fetching the live docs pages) and used noticeably more tokens. That's the expected cost of checking real documentation instead of guessing.

The strongest single result was the guild-condition eval — the skill traced the report to something neither the eval's author nor the baseline anticipated: `HasGuildWithName` and the rest of the Guild condition family only function if a separate third-party guild mod is installed, and it confirmed that against [Known gaps](https://kg-marketplace.pages.dev/reference/known-gaps/) before presenting it as the likely cause rather than a guess.

## Reproducing

Uses [`skill-creator`](https://agentskills.io) methodology (spawn with-skill + baseline runs per eval, grade each against `evals.json`'s assertions, aggregate). The raw run outputs, per-assertion grading, and aggregated benchmark aren't checked into this repo — they're regenerated each time, in a workspace directory alongside the skill (not inside it, so they don't pollute what gets loaded).
