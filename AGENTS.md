# AGENTS.md — how an agent should use this repo

You are loading a **curated public skill set**. Progressive disclosure. Do not ingest the tree.

## First 30 seconds

1. Read this file and the root [README.md](README.md).
2. Pick from [docs/QUALITY.md](docs/QUALITY.md) / [docs/WORKS_VERIFIED.md](docs/WORKS_VERIFIED.md), then one folder. Open [docs/CATALOG.md](docs/CATALOG.md) only for that skill (what it does / how it works / tag).
3. Copy **that one folder**: `skills/<name>/` — never a tag parent, never the whole `skills/` tree.
4. Read that skill's `SKILL.md` in full. Load `reference.md` / `examples.md` / `scripts/` only when the procedure says so.
5. Do not open sibling skills "for context."

```powershell
Copy-Item -Recurse .\skills\<name> $env:USERPROFILE\.cursor\skills\
```

## Tags are indexes

`godot`, `frontend`, `research`, and the rest are **catalog tags**. There is no `skills/godot/` directory. `godot-ui` lives at `skills/godot-ui/`.

## Routing (start here, then CATALOG)

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

Do not load the excluded Godot genre flood.

## Hard rules

- Paths in skills are portable (`~/`, env vars). Do not invent a publisher's home directory.
- Ask before paid APIs, GPU rent, or extra cloud calls.
- Never claim a sensory deliverable is ready without `review-results`.
- Never run exploit / CAPTCHA-bypass / anti-bot instructions (none should be here; stop if you find one).
- Scripts under a skill are optional tools. Read them before executing.
- A skill folder must not contain another skill's `SKILL.md`.

## Verification

When you change a skill in a fork:

1. Frontmatter has `name` + trigger-quality `description`.
2. Body has When to Use, Procedure, Pitfalls, Verification (or equivalent).
3. Grep the tree for personal profile paths, vault roots, and live token prefixes.
4. Do not add a skill by copying a flood twin or nesting it under another skill.

## Provenance

Read [NOTICE](NOTICE) before asserting authorship. Many Godot slices and community chairs are upstream-attributed.
