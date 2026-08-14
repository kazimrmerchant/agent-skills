# AGENTS.md — how an agent should use this repo

You are loading a **curated public skill pack**. Progressive disclosure. Do not ingest the tree.

## First 30 seconds

1. Read this file and the root [README.md](README.md).
2. Open [docs/CATALOG.md](docs/CATALOG.md) only to pick **one** skill.
3. Read that skill's `SKILL.md` in full. Load `reference.md` / `examples.md` / `scripts/` only when the procedure says so.
4. Do not open sibling skills "for context."

## Packs

| Pack | Path | Use when |
|------|------|----------|
| agent-os | `skills/agent-os/` | Goal/loop, quality pass, git/PR, research, local media, browser, frontend chairs |
| godot | `skills/godot/` | Godot 4.x implementation. Start at `game-godot` if the task is "do Godot," then the matching slice |

Later packs are names in [ROADMAP.md](ROADMAP.md) — they are **not** in this clone.

## Routing (agent-os)

| User intent | Skill |
|-------------|-------|
| `/goal`, until-done, GOAL.md | `goal` |
| `/better`, polish, harden | `better` |
| `/scale`, N≥10 unique | `quality-at-scale` |
| `/reviewresults`, ship a video/UI/SVG | `review-results` |
| git / commit / rebase | `git-workflow` |
| open or merge a PR | `github-pr-workflow` |
| research / due diligence | `end-to-end-research` |
| Obsidian notes | `obsidian` |
| Chrome / CDP / Playwright drive | `chrome-browser-automation` |
| Grok Imagine | `grokimagine` |
| Shorts production | `yt-shorts-flow-director` |
| Local I2V / stills | `localvideo` / `localimage-stills` / `local-media-router` |
| ComfyUI | `comfyui` / `comfyui-workflow-builder` |
| Ollama Cloud GLM | `ollama` |
| Three.js (ambitious) | `threejs-skill-router` (specialists may be absent in v1) |
| MCP server | `mcp-server-authoring` |
| Debug any failure | `systematic-debugging` |

## Routing (godot)

Read `game-godot` for test/export/CI. Then **one** slice:

| Need | Skill |
|------|-------|
| GDScript style / types / signals | `godot-gdscript-mastery` or `godot-gdscript-patterns` |
| C# | `game-csharp-godot` |
| 2D move / physics | `godot-characterbody-2d` / `godot-2d-physics` |
| Animation | `godot-animation-player` / `godot-animation-tree-mastery` / `godot-2d-animation` |
| 3D look | `godot-3d-lighting` / `godot-3d-materials` / `godot-3d-world-building` |
| UI | `godot-ui` |
| Architecture | `godot-autoload-architecture` / `godot-composition` / `godot-state-machine-advanced` |
| Systems | `godot-save-load-systems` / `godot-combat-system` / `godot-dialogue-system` / `godot-camera-systems` / `godot-audio-systems` |
| Ship | `godot-export-builds` / `godot-debugging-profiling` |

Do not load the 20+ genre files that were excluded from this pack.

## Hard rules

- Paths in skills are portable (`~/`, env vars). Do not invent a publisher's home directory.
- Ask before paid APIs, GPU rent, or extra cloud calls.
- Never claim a sensory deliverable is ready without `review-results`.
- Never run exploit / CAPTCHA-bypass / anti-bot instructions (none should be here; stop if you find one).
- Scripts under a skill are optional tools. Read them before executing.

## Verification

When you change a skill in a fork:

1. Frontmatter has `name` + trigger-quality `description`.
2. Body has When to Use, Procedure, Pitfalls, Verification (or equivalent).
3. Grep the tree for personal profile paths, vault roots, and live token prefixes.
4. Do not add a skill by copying a flood twin.

## Provenance

Read [NOTICE](NOTICE) before asserting authorship. Many Godot slices are upstream community skills.
