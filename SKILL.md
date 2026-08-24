---
name: kg-marketplace-skill
description: Author and validate config files for the Valheim mod "Marketplace and Server NPCs (Revamped)" — quests, NPCs, dialogues, traders, factions, and every other Configs/ system the mod ships. Use when a server admin asks to add, edit, or debug an entry in any of that mod's .cfg files, on any server (this skill carries no opinion on a specific server's own balance or economy).
user-invocable: true
---

# KG Marketplace Skill

Helps write and validate config files for the Valheim mod **Marketplace and Server NPCs (Revamped)**. General-purpose: covers the mod's config *format* and *systems*, not any one server's balance choices — a different server's quest-reward philosophy or shop prices are none of this skill's business.

Canonical reference for everything below: [kg-marketplace.pages.dev](https://kg-marketplace.pages.dev/), also fetchable in one shot as [llms-full.txt](https://kg-marketplace.pages.dev/llms-full.txt) (~45k tokens, the whole corpus) or per-page as `<page-url>index.md`. This skill doesn't vendor a copy of that corpus — it stays current on its own, so linking out beats duplicating it here and drifting stale.

## When to use

- Writing or editing a `.cfg` file under any mod's `Configs/` subfolder (quests, NPCs, dialogues, traders, factions, bankers, gamblers, buffers, transmog, teleporters, territories, server infos, player tags, and the rest)
- Debugging why a config entry silently isn't working in-game
- Explaining what a specific field, condition, command, or reward type does
- Setting up a new system on a server for the first time (e.g. "add a trader NPC", "set up a quest chain")

Not for: balancing rewards/difficulty for a specific server's own economy (that's server-specific taste, out of scope here), or anything about the mod's C# internals beyond what's needed to explain observed config behavior.

## Core syntax (applies to nearly every config type)

- Plain text, one entry per line. `#` starts a comment; blank lines are ignored.
- `[Section]` starts a new profile/block. Spacing and capitalization inside brackets are folded away before matching (`.ToLower()`/`.ToLowerInvariant()` internally, confirmed across every module's parser) — **except** Territories, where the header is case-sensitive and keeps spaces literally (confirmed: `TerritorySystem_Main_Server.cs` never lowercases the section name). The docs site also lists Leaderboard Achievements as case-sensitive, but its own parser calls `.ToLower()` on the header before hashing it (`Leaderboard_Server_Main.cs:131`) — that claim doesn't hold up against the source; treat it as case-insensitive there too until the docs are corrected. Case-folding cuts both ways: `[MyProfile]` and `[myprofile]` referencing each other always works, but it also means two *intentionally different* profiles that only differ by case or spacing silently collide into one — see pitfalls below.
- File names don't matter; folder + `[Section]` headers do. One config type can span multiple files and subfolders — everything in a folder is read together.
- Lines before any `[Section]` land in a profile literally called `default` (intentional, several NPC types default to it).
- Any script (Cyrillic, Chinese, etc.) is valid inside a header name.
- Three separators recur everywhere: `,` between fields in one entry (`Wood, 10, 1`), `|` between multiple entries on one line (`Wood, 10 | Stone, 5`), `||` for OR inside one condition group (only in requirement fields). Spaces around any of these are stripped — write them however is readable.
- A field that needs a literal space (an NPC's full name, a sentence) must be wrapped in `"double quotes"` — the quotes are stripped, the space survives.
- Most formats fail **silently per-entry**, not per-file, logged to the server console. Quests are stricter: one broken line skips the whole quest. A count/amount of `0` or negative usually becomes `1` instead of erroring — a common silent-mistake source.

Full page: [Config file syntax](https://kg-marketplace.pages.dev/concepts/config-syntax/).

## Procedure

1. **Identify the system** the request touches — check the map below for the right config folder and doc page. If it's not obviously one system (e.g. "an NPC that sells things and gives quests"), it's usually several config types working together (an NPC + a Trader profile + a Quest Profile), not one.
2. **Read that system's doc page** before writing anything — field order, separators, and defaults vary per config type, and guessing from a similar-looking type is a common source of subtly wrong output. Prefer fetching the live page over relying on training data — the mod gets updated, and stale field lists are a real recurring problem (see [Migrations](https://kg-marketplace.pages.dev/reference/migrations/) for past breaking changes).
3. **Check [Known gaps](https://kg-marketplace.pages.dev/reference/known-gaps/)** for that system before relying on anything that seems off, non-obvious, or too-good-to-be-true — several mod behaviors don't match their own name or documentation elsewhere (examples below). This page is checked directly against the mod's source, not guessed.
4. **Write the entry**, following [Content creation](https://kg-marketplace.pages.dev/concepts/content-creation/) and [Profiles](https://kg-marketplace.pages.dev/concepts/profiles/) for how content gets grouped and assigned to NPCs.
5. **Place the file** under the correct `Configs/<Subfolder>/` per [File structure](https://kg-marketplace.pages.dev/setup/file-structure/) — the parser only reads files from the right folder, regardless of filename.
6. **Verify**: the mod hot-reloads most config changes (see [Hot reload](https://kg-marketplace.pages.dev/setup/hot-reload/) for what does and doesn't pick up live) — check the server console for parse errors/warnings after saving, since most mistakes fail silently rather than crashing.

## Systems map

| Category | System | Doc page |
|---|---|---|
| Core | NPC system, Marketplace Hammer, Saved NPCs, Random NPC Speech | [npc/npc-system](https://kg-marketplace.pages.dev/npc/npc-system/) |
| Core | Quests, Quest Profiles, Quest Events | [configs/quests](https://kg-marketplace.pages.dev/configs/quests/) |
| Core | Dialogues, Custom Spawn Data | [configs/dialogues](https://kg-marketplace.pages.dev/configs/dialogues/) |
| Core | Factions | [configs/factions](https://kg-marketplace.pages.dev/configs/factions/) |
| Economy | Traders | [configs/traders](https://kg-marketplace.pages.dev/configs/traders/) |
| Economy | Bankers | [configs/bankers](https://kg-marketplace.pages.dev/configs/bankers/) |
| Economy | Gamblers | [configs/gamblers](https://kg-marketplace.pages.dev/configs/gamblers/) |
| Economy | Buffers, Buffer Profiles | [configs/buffers](https://kg-marketplace.pages.dev/configs/buffers/) |
| Economy | Transmogrification | [configs/transmogrification](https://kg-marketplace.pages.dev/configs/transmogrification/) |
| World | Server Info | [configs/server-infos](https://kg-marketplace.pages.dev/configs/server-infos/) |
| World | Teleporters | [configs/teleporters](https://kg-marketplace.pages.dev/configs/teleporters/) |
| World | Territories | [configs/territories](https://kg-marketplace.pages.dev/configs/territories/) |
| Server utilities | Player Tags | [configs/player-tags](https://kg-marketplace.pages.dev/configs/player-tags/) |
| Server utilities | Synced Localizer, Localization keys | [configs/synced-localizer](https://kg-marketplace.pages.dev/configs/synced-localizer/) |
| Server utilities | Distanced UI | [configs/distanced-ui](https://kg-marketplace.pages.dev/configs/distanced-ui/) |
| Server utilities | Discord Webhooks | [configs/discord-webhooks](https://kg-marketplace.pages.dev/configs/discord-webhooks/) |
| Server utilities | Leaderboard Achievements | [configs/leaderboard-achievements](https://kg-marketplace.pages.dev/configs/leaderboard-achievements/) |
| Shared concepts | Conditions (requirement language) | [concepts/conditions](https://kg-marketplace.pages.dev/concepts/conditions/) |
| Shared concepts | Commands (action language) | [concepts/commands](https://kg-marketplace.pages.dev/concepts/commands/) |
| Shared concepts | Prefabs and text markup, Custom assets | [concepts/prefabs-and-assets](https://kg-marketplace.pages.dev/concepts/prefabs-and-assets/) |
| Shared concepts | Scheduled configs (time windows) | [concepts/time-windows](https://kg-marketplace.pages.dev/concepts/time-windows/) |

## Common pitfalls (verified against mod source, not guessed)

- **`Configs/Lootboxes/` does nothing.** The folder gets auto-created and even has a Discord webhook template, but nothing in the mod's code parses its contents into an actual lootbox. Don't build content around it.
- **`PlayerHasOneOfCustomDataKeys` means the opposite of its name** — true when the player's data *differs* from the referenced set on at least one value, not when they "have one of" the listed values. Its sibling `PlayerHasAllCustomDataKeys` behaves as named. Test with known values before relying on either.
- **`GiveBuff`'s duration argument is capped at 1 second** — any value ≥1 passed to it is silently clamped to 1. Set the buff's own duration on the buff itself instead and omit the argument.
- **`ConsoleCommand` runs anything**, not a mod-specific whitelist, and briefly force-enables debug mode for that call — including world-editing/cheat commands. Only use it in trusted content.
- **`Kill` vs `KillAndCollect` star-level fields don't mean the same thing.** `Kill`'s level field gets `+1` added internally before comparing; `KillAndCollect`'s doesn't — write the intended star count plus one on a `KillAndCollect` target.
- **Two profiles that only differ by case or spacing silently merge into one.** Section-name matching folds through `.ToLower()` (spaces stripped too, in most modules) before comparing — confirmed in Trader, Quests, NPC, Banker, Buffer, ServerInfo, Teleporter, Transmogrification, Gambler, Leaderboard, Factions, and DistancedUI's own parsers. `[Daily]` in one file and `[daily]` in another aren't two profiles that happen to reference each other correctly — they're the *same* profile, and their contents get combined, which is easy to hit by accident across files written by different people or at different times. Doesn't apply to Territories (confirmed case-sensitive there); Leaderboard Achievements is documented as case-sensitive too but its parser doesn't actually behave that way — see the core-syntax note above.

Full, current list: [Known gaps](https://kg-marketplace.pages.dev/reference/known-gaps/). Check it — this list will drift as the mod changes; the live page won't.

## Guides worth pointing an admin at

For anything that spans multiple systems, the docs site's own guides are already written and worked-through — link rather than re-explain: [Your first quest](https://kg-marketplace.pages.dev/guides/first-quest/), [Quest chains](https://kg-marketplace.pages.dev/guides/quest-chain/), [A branching dialogue tree](https://kg-marketplace.pages.dev/guides/dialogue-tree/), [Dialogue patterns](https://kg-marketplace.pages.dev/guides/dialogue-patterns/), [Tracking player state](https://kg-marketplace.pages.dev/guides/tracking-player-state/), [Shops, currency, and taxes](https://kg-marketplace.pages.dev/guides/shop-and-economy/), [Setting up a territory](https://kg-marketplace.pages.dev/guides/territory-setup/).
