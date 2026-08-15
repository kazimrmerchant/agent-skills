# Skill Slice — how an agent should use this repo

You are loading **Skill Slice**, a curated public skill set. Progressive disclosure. Do not ingest the tree.

## First 30 seconds

1. This file only (do not require README in the 30s).
2. Match the routing table OR open [docs/WORKS_VERIFIED.md](docs/WORKS_VERIFIED.md) and pick one of the 12.
3. Copy that one `skills/<name>/`. Never a tag parent. Never the whole `skills/` tree.
4. Read that `SKILL.md` in full. Load `reference.md` / `examples.md` / `scripts/` only when the procedure says so.
5. Open [docs/CATALOG.md](docs/CATALOG.md) only to look up a name not in the table. Do not ingest CATALOG. Do not start there.
6. [docs/QUALITY.md](docs/QUALITY.md) is method + ranks — not the install list.

```powershell
Copy-Item -Recurse .\skills\<name> $env:USERPROFILE\.cursor\skills\
```

## Tags are indexes

`godot`, `frontend`, `research`, and the rest are **catalog tags**. There is no `skills/godot/` directory. `godot-ui` lives at `skills/godot-ui/`.

## Routing

| User intent | Skill |
|-------------|-------|
| `/goal`, until-done, GOAL.md | `goal` |
| `/better`, polish, harden | `better` |
| `/scale`, N≥10 unique | `quality-at-scale` |
| `/reviewresults`, ship a video/UI/SVG | `review-results` |
| git / commit / rebase | `git-workflow` |
| open or merge a PR | `github-pr-workflow` |
| research / due diligence | `end-to-end-research` |
| “what’s happening now” (30-day community/GitHub) | **not in this repo** — `npx skills add mvanhorn/last30days-skill -g` (v3). Complementary, never nested. |
| Obsidian notes | `obsidian` |
| Chrome / CDP / Playwright drive | `chrome-browser-automation` |
| Grok Imagine | `grokimagine` |
| Shorts production | `yt-shorts-flow-director` |
| Local I2V / stills | `localvideo` / `localimage-stills` / `local-media-router` |
| ComfyUI | `comfyui` / `comfyui-workflow-builder` |
| Ollama Cloud GLM | `ollama` |
| Three.js (ambitious) | `threejs-skill-router` then one `threejs-*` sibling |
| MCP server | `mcp-server-authoring` or `create-skill` |
| Debug any failure | `systematic-debugging` |
| Godot 4 hub | `game-godot` then one `godot-*` sibling |

Routing is intent → folder, not a works claim. Rate the 12 in WORKS_VERIFIED.md first. Studio/GPU rows (`grokimagine`, `yt-shorts-flow-director`, `localvideo`) are untested and often machine-local.

Do not load the excluded Godot genre flood.

## Hard rules

- Paths in skills are portable (`~/`, env vars). Do not invent a publisher's home directory.
- Ask before paid APIs, GPU rent, or extra cloud calls.
- Never claim a sensory deliverable is ready without `review-results`.
- Never run exploit / CAPTCHA-bypass / anti-bot instructions (none should be here; stop if you find one).
- Scripts under a skill are optional tools. Read them before executing.
- A skill folder must not contain another skill's `SKILL.md`.
- Do not open sibling skills "for context."

## Verification

When you change a skill in a fork:

1. Frontmatter has `name` + trigger-quality `description`.
2. Body has When to Use, Procedure, Pitfalls, Verification (or equivalent).
3. Grep the tree for personal profile paths, vault roots, and live token prefixes.
4. Do not add a skill by copying a flood twin or nesting it under another skill.

## Provenance

Read [NOTICE](NOTICE) before asserting authorship. Many Godot slices and community skills are upstream-attributed.
