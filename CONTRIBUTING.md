# Contributing

## What CI actually checks — and what it can't

Three automated checks run on every push/PR:

- [`skillscheck`](https://github.com/agentskills/agentskills) — is `SKILL.md` well-formed per the [Agent Skills spec](https://agentskills.io/specification) (frontmatter, progressive-disclosure size budget, link/asset quality)?
- [`snyk-agent-scan`](https://github.com/snyk/agent-scan) — could `SKILL.md` itself carry a prompt-injection or tool-poisoning payload?
- [`lychee`](https://github.com/lycheeverse/lychee-action) — do the links `SKILL.md` points at still resolve?

None of these can tell you whether a claim in `SKILL.md` is *true*. "`HasGuildWithName` takes one argument" passes all three checks whether or not that's actually how the condition behaves in the current mod version. That's a gap no linter closes — it needs a human (or an agent doing real verification work) checking the claim against something authoritative.

## Verifying a claim before adding it

This skill covers the mod's config format and systems, not just what plausibly sounds right. Before adding or changing a factual claim in `SKILL.md`:

1. Check it against the mod's own source where you can, or against [kg-marketplace.pages.dev](https://kg-marketplace.pages.dev/) — its docs are themselves checked against the mod's source, particularly the [Known gaps](https://kg-marketplace.pages.dev/reference/known-gaps/) page, which exists specifically to record mod behavior that doesn't match its own docs or naming elsewhere.
2. State the fact plainly — no `[source: Foo.cs:123]`-style citations. This skill is public; a citation into a private repository is either meaningless to whoever reads it or, worse, a path to something that was never meant to be shared.
3. If a version-specific behavior changed recently, check [Migrations](https://kg-marketplace.pages.dev/reference/migrations/) and consider whether the skill should say "as of version X" the way [Quests](https://kg-marketplace.pages.dev/configs/quests/) does for `KillAndCollect`'s level field.

## Re-checking what's already there

There's no automated way to detect "SKILL.md said something true when it was written, and the mod changed underneath it" — the [changelog](https://kg-marketplace.pages.dev/reference/changelog/) is the signal to watch. Worth a skim after a mod update that touches systems this skill talks about (conditions, commands, quest types, reward types), re-verifying anything that changed against the current docs.

[Issues tagged `inaccuracy`](https://github.com/odin-sons/kg-marketplace-skill/issues?q=is%3Aissue+is%3Aopen+label%3Ainaccuracy) are reports of exactly this — the skill template for filing one asks for the mod version tested against, specifically so a fix can note when the claim stopped being true instead of just silently changing it.
