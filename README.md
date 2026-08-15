# Skill Slice

Copy **one** unique skill folder — a slice, not the pie. Do not copy a tag, a parent pack, or the whole tree.

**Start with the 12** that were Read-verified (procedure + claimed files, commands, or APIs). That is not a runtime bake. Untested copies are not a claim they work.

**[docs/WORKS_VERIFIED.md](docs/WORKS_VERIFIED.md)** — rate these first.

**Browse all unique chairs:** [Skill Slice explorer](https://kazimrmerchant.github.io/skill-slice/) (search, filter, copy one install command). Do **not** `--all`.

```bash
npx skills add kazimrmerchant/skill-slice --skill better
```

Also: `npx skills add kazimrmerchant/skill-slice@better`

Swap `better` for any name on that list. Do **not** `--all`. Do **not** `--skill '*'`.

Fallback (clone, then one folder):

```powershell
git clone https://github.com/kazimrmerchant/skill-slice.git skill-slice
Copy-Item -Recurse .\skill-slice\skills\<name> $env:USERPROFILE\.cursor\skills\
```

Other hosts: copy that same `skills/<name>/` folder into the product’s skills root (`SKILL.md` required). Never install into `~/.cursor/skills-cursor/` (Cursor-managed built-ins).

Progressive-disclosure skills (`name` + `description` trigger; body loads when the task matches) for Cursor, Claude, Hermes, and other [agentskills.io](https://agentskills.io) hosts. Tags (`godot`, `frontend`, `research`, …) are **catalog indexes only** — not parent directories.

## Inventory

**495** unique sibling folders on disk. **12** works-verified. The rest are untested copies — not a claim they work. Read sample was **N=24**, not a bake of the tree. Method: [docs/QUALITY.md](docs/QUALITY.md). Human browse: [explorer](https://kazimrmerchant.github.io/skill-slice/).

GitHub has no file-pin API; the repo homepage URL plus [docs/WORKS_VERIFIED.md](docs/WORKS_VERIFIED.md) is the pin.

## For agents

Read [AGENTS.md](AGENTS.md) first (30 seconds). Copy **one** folder. Read that `SKILL.md`. Do not ingest the tree. Do not start at [docs/CATALOG.md](docs/CATALOG.md).

## What this is

- Progressive-disclosure skills: `name` + `description` are the trigger; the body loads when the task matches.
- A uniqueness bar: independent `QUALITY_KEEP`, one capability owner, no flood families.
- A **works-bar** (executability): numbered procedure with real commands / files / APIs — not a stale encyclopedia. See [docs/INCLUSION.md](docs/INCLUSION.md#works-bar-executability). Rate [docs/WORKS_VERIFIED.md](docs/WORKS_VERIFIED.md) first.
- MIT for original docs and original skills. **Not every body is original** — see [NOTICE](NOTICE).
- Scorecard dimensions: [docs/VALUE_FRAMEWORK.md](docs/VALUE_FRAMEWORK.md).

## What is not included

- Private library-only skills
- Quarantine / dual-use offensive content
- Exact body-duplicate losers and flood families (`azure-*`, `godot-genre-*`, `performing-*`, `cursor-rules-for-*`, wordpress / seismic / pymatgen / book-sft twins)
- Nested mega-packs, `node_modules`, junctions, skills-inside-skills
- `art-direction-islamic-mv` (private)
- **last30days** — install the upstream skill instead (below). We do not vendor that engine.

Full include/exclude: [docs/INCLUSION.md](docs/INCLUSION.md).

## Companion: last30days (not in this set)

For “what’s happening now” across Reddit, HN, Polymarket, GitHub, and the web (last 30 days), install the upstream skill — do not expect it in `skills/`:

```powershell
npx skills add mvanhorn/last30days-skill
```

Upstream: [mvanhorn/last30days-skill](https://github.com/mvanhorn/last30days-skill) (MIT). Use **v3.x**. Free sources work without keys. Do not add paid API keys unless you choose to. Complementary to `end-to-end-research` in this repo (cited due-diligence). Never nest last30days inside another skill.

## Where to look

[AGENTS.md](AGENTS.md) · [docs/WORKS_VERIFIED.md](docs/WORKS_VERIFIED.md) · [docs/QUALITY.md](docs/QUALITY.md) · [docs/CATALOG.md](docs/CATALOG.md) (lookup) · [docs/INCLUSION.md](docs/INCLUSION.md) · [NOTICE](NOTICE) · [CONTRIBUTING.md](CONTRIBUTING.md) · [SECURITY.md](SECURITY.md) · optional [llms.txt](llms.txt)

## Security

- Review bundled scripts before running them.
- Do not paste API keys into chat or commit `.env`.
- `chrome-browser-automation` and `grokimagine` drive a **real** Chrome over CDP. They are not anti-bot / CAPTCHA tools.

PII greps and sanitize rules: [SECURITY.md](SECURITY.md). Published files are sanitized; this repo’s git history is not rewritten and is not claimed PII-clean.

## License

[MIT](LICENSE) for original material. Upstream / community skills: [NOTICE](NOTICE).

## Contributing

[CONTRIBUTING.md](CONTRIBUTING.md). New skills are sibling folders under `skills/<name>/`, tagged in the catalog — not nested pack parents.
