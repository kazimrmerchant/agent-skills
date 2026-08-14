# Agent Skills

**Rate these first:** the 12 works-verified chairs in [docs/WORKS_VERIFIED.md](docs/WORKS_VERIFIED.md). The uniqueness inventory is **487** sibling folders (target was 500; shipped 491 after uniqueness cuts; two missing-file contract cuts; two recommend-cut twins this wave). Quality over the integer. Untested copies are not a claim they work.

**Quality ranking:** method and ranks in [docs/QUALITY.md](docs/QUALITY.md). Ranks are not a claim that all 487 were re-read this week. Below-A improve playbook: [docs/QUALITY_IMPROVE.md](docs/QUALITY_IMPROVE.md).

Each skill is one folder. Copy **that folder**. Do not copy a tag, a parent pack, or the whole tree into an always-on set.

```
skills/<skill-name>/SKILL.md
```

Tags (`godot`, `frontend`, `research`, …) are **catalog indexes only** — not parent directories.

This is **not** a 250-file score drawer and **not** a 5,000-skill vault dump. Ratings beat raw count. See [docs/VALUE_FRAMEWORK.md](docs/VALUE_FRAMEWORK.md).

Layout follows the portable skill folder convention used by Cursor, Claude, and [agentskills.io](https://agentskills.io).

## What this is

- Progressive-disclosure skills: `name` + `description` are the trigger; the body loads when the task matches.
- A uniqueness bar: independent `QUALITY_KEEP`, one capability owner, no flood families.
- A **works-bar** (executability): numbered procedure with real commands / files / APIs — not a stale encyclopedia. See [docs/INCLUSION.md](docs/INCLUSION.md#works-bar-executability). 487 is unique chairs, not a verified-works count. Rate [docs/WORKS_VERIFIED.md](docs/WORKS_VERIFIED.md) first.
- Every skill has a **what / how** row in [docs/CATALOG.md](docs/CATALOG.md).
- MIT for original docs and original chairs. **Not every body is original** — see [NOTICE](NOTICE).

## What is not included

- Private library-only skills
- Quarantine / dual-use offensive content
- Exact body-duplicate losers and flood families (`azure-*`, `godot-genre-*`, `performing-*`, `cursor-rules-for-*`, wordpress / seismic / pymatgen / book-sft twins)
- Personal vault paths, live keys, owner emails
- Nested mega-packs, `node_modules`, junctions, skills-inside-skills
- `art-direction-islamic-mv` (private)
- **last30days** — install the upstream skill instead (below). We do not vendor that engine.

Full include/exclude: [docs/INCLUSION.md](docs/INCLUSION.md).

## Install one skill

Clone, then copy **one** sibling folder:

```powershell
git clone https://github.com/kazimrmerchant/agent-skills.git agent-skills
Copy-Item -Recurse .\agent-skills\skills\<name> $env:USERPROFILE\.cursor\skills\
```

Examples: `<name>` = `better`, `godot-ui`, `git-workflow`, `threejs-fundamentals`.

Project-local:

```
.cursor/skills/<skill-name>/SKILL.md
```

Never install into `~/.cursor/skills-cursor/` (Cursor-managed built-ins).

### Claude

```powershell
Copy-Item -Recurse .\agent-skills\skills\<name> $env:USERPROFILE\.claude\skills\
```

### Hermes / other agentskills.io hosts

Copy the same single folder into that product's skills root. One folder per skill; `SKILL.md` required.

An agent should **Read** [docs/CATALOG.md](docs/CATALOG.md), copy **one** folder, then Read that `SKILL.md`. Do not ingest the tree.

## Companion: last30days (not in this set)

For “what’s happening now” across Reddit, HN, Polymarket, GitHub, and the web (last 30 days), install the upstream skill — do not expect it in `skills/`:

```powershell
npx skills add mvanhorn/last30days-skill -g -a cursor
```

Upstream: [mvanhorn/last30days-skill](https://github.com/mvanhorn/last30days-skill) (MIT). Use **v3.x**. Free sources work without keys. Do not add paid API keys unless you choose to. Complementary to `end-to-end-research` in this repo (cited due-diligence). Never nest last30days inside another skill.

## Tag index (487 siblings)

Counts are folders on disk. Full what/how: [docs/CATALOG.md](docs/CATALOG.md).

| Tag | Count | Install when |
|-----|------:|--------------|
| agent-os | 27 | Goal/loop, quality, git/PR, local media, browser chairs |
| godot | 22 | Godot 4 curated slices (v1) |
| frontend | 38 | React/Next/CSS/a11y — unique chairs, not a 144-clone flood |
| threejs | 10 | Specialists `threejs-skill-router` points at |
| video | 25 | FFmpeg, Remotion, transcription, assembly |
| research | 22 | Methods beyond `end-to-end-research` |
| defensive-security | 12 | Audit / hardening / Semgrep / Snyk — not red-team flood |
| godot-extra | 17 | XR, multiplayer, shaders, addons, editor — not genre twins |
| mcp-and-tools | 12 | MCP / tool-design / `create-skill` |
| python-backend | 36 | FastAPI / Django / Python APIs |
| data-databases | 28 | SQL / Postgres / Redis / similar |
| ai-ml | 40 | LLM / RAG / eval / local inference |
| git-github | 8 | Git/GitHub beyond the two v1 chairs |
| testing-qa | 17 | Test / QA chairs |
| devops | 28 | Real CI/CD / deploy (no Azure flood) |
| automation | 12 | Orchestration |
| game-dev | 25 | Non-Godot game systems |
| mobile | 8 | Expo / iOS / Android / Maestro |
| svg-and-design | 6 | SVG / illustration extras |
| image-vision | 8 | Photo / vision |
| agents | 6 | Agents SDK / Gemini-class agent tools |
| content | 12 | Writing / SEO audit |
| comms | 12 | Integrations / messaging |
| architecture | 5 | Architecture patterns |
| auth | 6 | Auth / identity |
| docs-knowledge | 5 | Docs / knowledge bases |
| browser | 7 | Playwright extras; legitimate scraping only |
| science | 8 | Unique science/math (no pymatgen twins) |
| product | 6 | Product / GTM / experiments |
| finance | 4 | Markets / finance |
| networking | 5 | Network / API triage |
| localization | 2 | i18n |
| ecommerce | 3 | Payments / commerce |
| observability | 5 | Metrics / tracing / cost guards |

## Quality bar

| Dimension | Meaning |
|-----------|---------|
| Clarity | `name` + `description` fire correctly; when / not-for |
| Actionability | Works-bar: unique owner **and** a numbered procedure an agent can execute (real commands / files / APIs) **and** not a stale encyclopedia / twin / flood |
| Completeness | Enough to execute; depth in `references/` |
| Uniqueness | One capability owner |
| Demand | Real workflows, not genre spam |
| Safe-to-publish | No exploits, secrets, or personal paths |
| Maint | Stable enough to update |

v1 chairs were read and sanitized. The expansion wave is **copied + path/email sanitized** from independently graded `QUALITY_KEEP` bodies — not rewritten by a bulk script. **Do not claim all 487 work.** Rate the 12 in [docs/WORKS_VERIFIED.md](docs/WORKS_VERIFIED.md) first. A 2026-08-14 stratified sample of 18 is in [docs/INCLUSION.md](docs/INCLUSION.md#works-bar-executability); two FAIL folders were cut; two recommend-cut twins were cut later; remaining **471** are untested copies. Further body upgrades continue in waves.

## Security

- Review bundled scripts before running them.
- Do not paste API keys into chat or commit `.env`.
- `chrome-browser-automation` and `grokimagine` drive a **real** Chrome over CDP. They are not anti-bot / CAPTCHA tools.

## License

[MIT](LICENSE) for original material. Upstream / community skills: [NOTICE](NOTICE).

## Contributing

[CONTRIBUTING.md](CONTRIBUTING.md). New skills are sibling folders under `skills/<name>/`, tagged in the catalog — not nested pack parents.
