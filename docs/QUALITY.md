# Quality ranking

**Date:** 2026-08-14  
**Generation SHA:** `5696afd5fab8514cc4e072212dbae8f3a95c6000` (`5696afd`)  
**Inventory:** 489 sibling folders under `skills/<name>/SKILL.md`

This sheet is **method first**, then ranks. Rate the 12 works-verified chairs before treating a high PQI as a “it works” claim.

- Full 489 rows (machine-readable): [quality-ranking.csv](quality-ranking.csv)
- Full 489 rows (GitHub-renderable, by works band): [quality-ranking.md](quality-ranking.md)
- Works-verified subset: [WORKS_VERIFIED.md](WORKS_VERIFIED.md)
- Inclusion / works-bar: [INCLUSION.md](INCLUSION.md)
- Scorecard dimensions: [VALUE_FRAMEWORK.md](VALUE_FRAMEWORK.md)
- What/how catalog (not ranks): [CATALOG.md](CATALOG.md)

## Rate / install first

These **12 WORKS** are ranks **1–12** after the works-first sort (Read sample 2026-08-14). Install and rate this set before treating any high PQI as a works claim.

| rank | skill | tag | pqi | Read sample 2026-08-14 |
| --- | --- | --- | ---: | --- |
| 1 | `better` | agent-os | 100.0 | Numbered 0–5 polish loop (`BETTER:` report). |
| 2 | `ollama-local-setup` | ai-ml | 95.9 | Numbered Windows Ollama install; `scripts/verify-ollama.ps1` exists. |
| 3 | `stable-diffusion` | ai-ml | 94.8 | Numbered HuggingFace Diffusers pipelines (txt2img, ControlNet, LoRA). |
| 4 | `mcp-server-authoring` | agent-os | 94.2 | Numbered MCP transport/tool-design; inspector + templates. |
| 5 | `godot-ui` | godot | 94.0 | Godot 4.3+ Control/Theme/focus GDScript; layout ref present. |
| 6 | `chroma` | ai-ml | 93.1 | Numbered chromadb `create_collection` / `add` / `query`. |
| 7 | `git-workflow` | agent-os | 93.1 | Numbered PowerShell git/gh; companions present. |
| 8 | `threejs-fundamentals` | threejs | 93.1 | Numbered scene/camera/renderer/Object3D scaffold. |
| 9 | `weights-and-biases` | ai-ml | 93.1 | Numbered `wandb.init` / `log` / `sweep`; refs present. |
| 10 | `docker-management` | devops | 92.0 | Numbered `docker` / `compose` lifecycle. |
| 11 | `emil-design-eng` | frontend | 91.5 | Numbered animation craft with real CSS/JS APIs. |
| 12 | `github-actions-advanced` | git-github | 88.2 | PowerShell discovery then SHA-pinned workflow YAML. |

### WEAK (ranks 13–16) — caveats, not install-first

Kept in the Read sample with caveats. **Not** an install-first set. Evidence: [INCLUSION.md](INCLUSION.md#works-bar-executability).

| rank | skill | tag | pqi | Caveat |
| --- | --- | --- | ---: | --- |
| 13 | `angular-state-management` | frontend | 79.6 | Snippets miss imports; injects `HttpClient` then uses `fetch`. |
| 14 | `glassmorphism` | frontend | 78.5 | Copy-paste CSS; labels itself a `design-it` child, not a direct trigger. |
| 15 | `energy-procurement` | ai-ml | 77.4 | Domain C&I playbook with real formulas; not a shell procedure. |
| 16 | `shadcn` | frontend | 76.2 | CLI procedure is real; claimed `references/cli.md` lives at skill root. |

## How assessed

Three layers. They are not interchangeable.

1. **Independent (July 2026).** `independent_score` / `independent_grade` / `independent_decision` are the 2026-07-17 `qwen36_27b` re-grade of the **private** library (2,540 skills, mean **69.2**). Joined here by folder name = CSV `skill`. The public tree is mostly the `QUALITY_KEEP` slice of that re-grade, plus v1 chairs. **This wave did not re-score 489 bodies with a model.** A high independent score is a July library grade, not a 2026-08-14 reread.
2. **Structural (today).** `structural_now` is a 2026-08-14 mechanical scan of **this repo’s** `SKILL.md` files (frontmatter length/signals, numbered procedure, claimed `references/` / `scripts/` paths on disk, lean line count, YAML `name` vs folder). No LLM. Thresholds below are falsifiable.
3. **Works overlay (Read sample only).** `works` is **N=18** from 2026-08-14: **12 WORKS**, **4 WEAK**, **2 FAIL** (already cut from git). Everyone else is `untested`. Untested ≠ fail. Untested ≠ works. **Do not claim all 489 are verified-works.**

PQI blends those layers with the formula below. It is **not** “we re-read 489 today.” It is **not** an agy/encyclopedia CLI label. It is **not** a VALUE_FRAMEWORK rescore of this week’s bodies.

### How to read ranks

Rank 1 = highest recommended under the **sort below**. PQI is compared **inside** a works band, not above it.

- **Ranks 1–12** are the Read-sample **WORKS**, sorted by PQI inside that band (then independent_score, then name). This is the install/rate-first set.
- **Ranks 13–16** are the Read-sample **WEAK** (keep with caveats; not install-first).
- **Ranks 17–489** are **untested** — **not an install order**. PQI there is the July independent score plus today’s structural scan only. Untested ≠ fail. Untested ≠ works.

This wave did **not** re-score 489 bodies with a model.

- `WORKS` — Read-sample pass (procedure an agent can execute).
- `WEAK` — Read-sample keep with caveats (see INCLUSION).
- `untested` — not in the N=18 sample. High PQI here is structure and/or the July score.

v1 chairs with **no** CSV row have empty `independent_*` (displayed as —), **not** a stamped `0`.

Untested PQI = 100 means `structural_now` = 100 (no July row), **not** a works claim — those rows now sit **after** the WORKS and WEAK bands.

## VALUE_FRAMEWORK ↔ independent q-map

Document mapping only. This is **not** a new 2026-08-14 reread of 489 files. Independent subscores are the July `q1`–`q7` fields from that private re-grade.

| VALUE_FRAMEWORK | Independent subscore |
|-----------------|----------------------|
| Clarity | q4_trigger |
| Actionability | q3_procedure |
| Completeness | q2_utility |
| Uniqueness | q5_unique |
| Demand | *(no 1:1; leave blank)* |
| Safe-to-publish | q7_publish + q1_truth |
| Maint | q6_fresh |

## Structural scan (falsifiable)

`structural_now` = `description_points` + `procedure_points` + `claimed_paths_points` + `lean_points` + `name_points` (0–100). Current public `SKILL.md` only.

**description_points (0 / 15 / 30)**

- Parse YAML frontmatter `description` (handle `>` folded and `|` and quoted). Char count = stripped unfolded text length.
- when-signal (case-insensitive, in description): `\bwhen\b` OR `use for` OR `trigger`
- not-for-signal (case-insensitive, in description): `not for` OR `do not use` OR `not a ` OR `never ` OR `not /`
- +30 if len ≥ 80 AND when-signal AND not-for-signal
- +15 if description len ≥ 1 but not both length+signals
- +0 if empty/missing description

Signals are **description-only**. A body “When / Do not use” table does not count. Example: `git-workflow` has when in YAML and not-for in a body table → `has_when_not_for=no`, description_points = 15.

**procedure_points (0 / 30)**

- +30 if SKILL.md has **≥ 3** lines matching (multiline):
  `^\s*(?:\d+[\.\)]\s+\S|\*\*\d+[\.\)]\*\*|\d+\.\s+\S)`
  OR ≥ 3 headings matching `(?im)^#{2,4}\s+(?:\d+[\.\)]|step\s+\d+)`
- else 0

**claimed_paths_points (0 / 20)**

- From SKILL.md, collect unique relative paths matching `(?:references|scripts)/[A-Za-z0-9._/-]+` (strip trailing punctuation/` )`). Ignore http(s) URLs.
- Resolve each against the skill folder. If **any** claimed file/dir is missing → **0**. If none claimed, or all exist → **20**.
- Root-level `reference.md` / `examples.md` (no `references/` prefix) are **not** this check. Vacuous 20 is OK.

This flag is **not** the works overlay. Lean copies often mention companions that were not copied. `github-actions-advanced` is WORKS in the Read sample and still `claimed_paths_ok=no` (example `scripts/*.sh` in the body are not in the folder). `shadcn` is WEAK for the same class of miss (`references/cli.md` vs files at skill root).

**lean_points (0 / 10)**

- +10 if SKILL.md line count (splitlines) is **40–700 inclusive**, else 0

**name_points (0 / 10)**

- +10 if YAML `name` equals folder name (strip quotes). Else 0.
- This wave: only `transformers.js` misses (YAML `name` is `transformers-js`).

## PQI formula

Round PQI to 1 decimal.

```
independent = my_score if CSV hit else null

works_overlay:
  WORKS = 100
  WEAK = 55
  untested = omitted from this term (do not treat as 50)

If CSV hit AND untested:
  PQI = 0.70 * independent + 0.30 * structural_now
If CSV hit AND WORKS:
  PQI = 0.50 * independent + 0.20 * structural_now + 0.30 * 100
If CSV hit AND WEAK:
  PQI = 0.50 * independent + 0.20 * structural_now + 0.30 * 55
If no CSV (v1 chairs) AND WORKS:
  PQI = 0.40 * structural_now + 0.60 * 100
If no CSV AND WEAK:
  PQI = 0.40 * structural_now + 0.60 * 55
If no CSV AND untested:
  PQI = structural_now
```

**Sort (rank 1 = highest recommended):**

1. works: **WORKS**, then **WEAK**, then **untested**
2. PQI descending
3. independent_score descending (nulls last)
4. skill name A-Z

Tags come from [CATALOG.md](CATALOG.md) section headers `## <tag> (N)` and the first-column skill names — **not** the private CSV `pack` column (polluted). Every public folder has exactly one tag.

## Columns (what they are / are not)

| Column | Is | Is not |
|--------|----|--------|
| `rank` | Sort order under the keys above | A claim the body was re-read this week |
| `skill` | Folder name | |
| `tag` | CATALOG section | CSV `pack` |
| `pqi` | Blend of layers; 1 decimal | A new model score |
| `independent_score` | July `my_score`, or empty | Invented for v1 misses; empty ≠ 0 |
| `independent_grade` | July `my_grade`, or empty | |
| `independent_decision` | July `my_decision`, or empty | A 2026-08-14 keep/drop |
| `works` | Read-sample overlay | Structural `claimed_paths_ok` |
| `skill_md_lines` | `splitlines()` count | Token count |
| `description_chars` | Stripped unfolded YAML description length | Body length |
| `has_when_not_for` | Both description signals | Body tables |
| `has_numbered_procedure` | Procedure rule above | “Looks procedural” |
| `claimed_paths_ok` | All matched `references/` / `scripts/` exist, or none claimed | Root `reference.md` |
| `name_matches_folder` | YAML `name` == folder | Display title |
| `structural_now` | Sum of point rules | Independent score |
| `notes` | v1 miss / non-KEEP still in tree | |

## Coverage

**456 / 33** — 456 folders matched a July CSV `skill` row; **33** v1 chairs have no independent row (no invented `my_score`).

Hit independent scores: min **53.3** / mean **91.0** / max **97.8** (public set is the KEEP-heavy slice; the 2,540-row private mean was **69.2**).

**Misses (independent columns empty):**  
`addvalue`, `better`, `goal`, `godot-2d-animation`, `godot-2d-physics`, `godot-3d-lighting`, `godot-3d-materials`, `godot-3d-world-building`, `godot-animation-player`, `godot-animation-tree-mastery`, `godot-audio-systems`, `godot-autoload-architecture`, `godot-camera-systems`, `godot-characterbody-2d`, `godot-combat-system`, `godot-composition`, `godot-debugging-profiling`, `godot-dialogue-system`, `godot-export-builds`, `godot-gdscript-mastery`, `godot-save-load-systems`, `godot-state-machine-advanced`, `godot-ui`, `grokimagine`, `inspire-quote-effects`, `local-media-router`, `localimage-stills`, `localvideo`, `quality-at-scale`, `review-results`, `threejs-skill-router`, `transition`, `yt-shorts-flow-director`

Spot-check vs July CSV: `git-workflow` 92.2 A QUALITY_KEEP; `chroma` 92.2 A; `glassmorphism` 90.0 A; `stable-diffusion` 95.6 A+; `better` has no independent row.

## Independent non-KEEP still in tree

July `independent_decision` was **not** `QUALITY_KEEP`; still in tree **pending review**. Do not hide them.

| Skill | Decision | Grade | independent_score |
|-------|----------|-------|------------------:|
| `chrome-browser-automation` | LIBRARY_ONLY | C | 63.3 |
| `comfyui-workflow-builder` | LIBRARY_ONLY | B- | 74.4 |
| `context-optimization` | LIBRARY_ONLY | C+ | 66.7 |
| `godot-gdscript-patterns` | DROP | D | 53.3 |
| `illustration-direction` | MERGE_OR_DROP | C+ | 65.6 |
| `senior-frontend` | MERGE_OR_DROP | C+ | 67.8 |
| `svg-quality-audit` | DROP | D | 57.8 |

## Summary histograms

**Independent grade** (— = the 33 misses):

| Grade | Count |
|-------|------:|
| A+ | 61 |
| A | 236 |
| A- | 149 |
| B+ | 3 |
| B- | 1 |
| C+ | 3 |
| C | 1 |
| D | 2 |
| — | 33 |

**Works overlay:** WORKS 12 · WEAK 4 · untested 473.

**PQI bands:**

| Band | Count |
|------|------:|
| ≥ 90 | 181 |
| 80–89.9 | 282 |
| 70–79.9 | 18 |
| < 70 | 8 |

**Structural (this scan):** numbered procedure 486/489; both description when+not-for signals 26/489; `claimed_paths_ok=no` **87** (names in [quality-ranking.csv](quality-ranking.csv)); YAML name mismatch 1 (`transformers.js`).

## Ranked tables

Ranks **1–50** (works-first) and bottom **25** here. Full **489**: [quality-ranking.csv](quality-ranking.csv) and [quality-ranking.md](quality-ranking.md).

This is **not** a 50-item install list. Ranks 1–12 WORKS (install/rate first), 13–16 WEAK (caveats), **17–50 untested** (July score + structure only — not an install order).

### Ranks 1–50 (works-first; 17+ not install order)

| rank | skill | tag | pqi | independent_score | independent_grade | independent_decision | works | notes |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | `better` | agent-os | 100.0 | — | — | — | WORKS | v1 chair; no independent CSV row |
| 2 | `ollama-local-setup` | ai-ml | 95.9 | 97.8 | A+ | QUALITY_KEEP | WORKS | — |
| 3 | `stable-diffusion` | ai-ml | 94.8 | 95.6 | A+ | QUALITY_KEEP | WORKS | — |
| 4 | `mcp-server-authoring` | agent-os | 94.2 | 94.4 | A | QUALITY_KEEP | WORKS | — |
| 5 | `godot-ui` | godot | 94.0 | — | — | — | WORKS | v1 chair; no independent CSV row |
| 6 | `chroma` | ai-ml | 93.1 | 92.2 | A | QUALITY_KEEP | WORKS | — |
| 7 | `git-workflow` | agent-os | 93.1 | 92.2 | A | QUALITY_KEEP | WORKS | — |
| 8 | `threejs-fundamentals` | threejs | 93.1 | 92.2 | A | QUALITY_KEEP | WORKS | — |
| 9 | `weights-and-biases` | ai-ml | 93.1 | 92.2 | A | QUALITY_KEEP | WORKS | — |
| 10 | `docker-management` | devops | 92.0 | 90.0 | A | QUALITY_KEEP | WORKS | — |
| 11 | `emil-design-eng` | frontend | 91.5 | 88.9 | A- | QUALITY_KEEP | WORKS | — |
| 12 | `github-actions-advanced` | git-github | 88.2 | 94.4 | A | QUALITY_KEEP | WORKS | — |
| 13 | `angular-state-management` | frontend | 79.6 | 92.2 | A | QUALITY_KEEP | WEAK | — |
| 14 | `glassmorphism` | frontend | 78.5 | 90.0 | A | QUALITY_KEEP | WEAK | — |
| 15 | `energy-procurement` | ai-ml | 77.4 | 87.8 | A- | QUALITY_KEEP | WEAK | — |
| 16 | `shadcn` | frontend | 76.2 | 93.3 | A | QUALITY_KEEP | WEAK | — |
| 17 | `goal` | agent-os | 100.0 | — | — | — | untested | v1 chair; no independent CSV row |
| 18 | `grokimagine` | agent-os | 100.0 | — | — | — | untested | v1 chair; no independent CSV row |
| 19 | `localvideo` | agent-os | 100.0 | — | — | — | untested | v1 chair; no independent CSV row |
| 20 | `game-xr-development` | godot-extra | 98.5 | 97.8 | A+ | QUALITY_KEEP | untested | — |
| 21 | `game-multiplayer-netcode` | game-dev | 97.7 | 96.7 | A+ | QUALITY_KEEP | untested | — |
| 22 | `editor-and-tooling` | godot-extra | 96.1 | 94.4 | A | QUALITY_KEEP | untested | — |
| 23 | `end-to-end-web-scraping` | browser | 96.1 | 94.4 | A | QUALITY_KEEP | untested | — |
| 24 | `game-performance-profiling` | game-dev | 96.1 | 94.4 | A | QUALITY_KEEP | untested | — |
| 25 | `game-steamworks-sdk` | game-dev | 96.1 | 94.4 | A | QUALITY_KEEP | untested | — |
| 26 | `game-unity-engine` | game-dev | 96.1 | 94.4 | A | QUALITY_KEEP | untested | — |
| 27 | `game-unreal-engine` | game-dev | 96.1 | 94.4 | A | QUALITY_KEEP | untested | — |
| 28 | `shaders-and-rendering` | godot-extra | 96.1 | 94.4 | A | QUALITY_KEEP | untested | — |
| 29 | `game-custom-physics-solvers` | game-dev | 95.3 | 93.3 | A | QUALITY_KEEP | untested | — |
| 30 | `audio-whisper-transcription` | video | 94.0 | 97.8 | A+ | QUALITY_KEEP | untested | — |
| 31 | `claimable-postgres` | data-databases | 94.0 | 97.8 | A+ | QUALITY_KEEP | untested | — |
| 32 | `claude-in-chrome-troubleshooting` | mcp-and-tools | 94.0 | 97.8 | A+ | QUALITY_KEEP | untested | — |
| 33 | `deploy-to-vercel` | devops | 94.0 | 97.8 | A+ | QUALITY_KEEP | untested | — |
| 34 | `deployments-cicd` | devops | 94.0 | 97.8 | A+ | QUALITY_KEEP | untested | — |
| 35 | `eas-update-insights` | mobile | 94.0 | 97.8 | A+ | QUALITY_KEEP | untested | — |
| 36 | `examprep-ai` | research | 94.0 | 97.8 | A+ | QUALITY_KEEP | untested | — |
| 37 | `expo-api-routes` | mobile | 94.0 | 97.8 | A+ | QUALITY_KEEP | untested | — |
| 38 | `expo-examples` | mobile | 94.0 | 97.8 | A+ | QUALITY_KEEP | untested | — |
| 39 | `geospatial-analysis` | data-databases | 94.0 | 97.8 | A+ | QUALITY_KEEP | untested | — |
| 40 | `git-hooks-automation` | git-github | 94.0 | 97.8 | A+ | QUALITY_KEEP | untested | — |
| 41 | `huggingface-lora-space-builder` | ai-ml | 94.0 | 97.8 | A+ | QUALITY_KEEP | untested | — |
| 42 | `huggingface-zerogpu` | python-backend | 94.0 | 97.8 | A+ | QUALITY_KEEP | untested | — |
| 43 | `nextjs-supabase-auth` | frontend | 94.0 | 97.8 | A+ | QUALITY_KEEP | untested | — |
| 44 | `node-inspect-debugger` | testing-qa | 94.0 | 97.8 | A+ | QUALITY_KEEP | untested | — |
| 45 | `papers-skill` | research | 94.0 | 97.8 | A+ | QUALITY_KEEP | untested | — |
| 46 | `peft` | ai-ml | 94.0 | 97.8 | A+ | QUALITY_KEEP | untested | — |
| 47 | `phaser-game-development` | game-dev | 94.0 | 97.8 | A+ | QUALITY_KEEP | untested | — |
| 48 | `pinggy-tunnel` | networking | 94.0 | 97.8 | A+ | QUALITY_KEEP | untested | — |
| 49 | `product-marketing` | product | 94.0 | 97.8 | A+ | QUALITY_KEEP | untested | — |
| 50 | `render-monitor` | devops | 94.0 | 97.8 | A+ | QUALITY_KEEP | untested | — |

### Bottom 25

| rank | skill | tag | pqi | independent_score | independent_grade | independent_decision | works | notes |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 465 | `odoo-qweb-templates` | docs-knowledge | 80.2 | 86.7 | A- | QUALITY_KEEP | untested | — |
| 466 | `remotion-video` | video | 80.2 | 86.7 | A- | QUALITY_KEEP | untested | — |
| 467 | `statsmodels` | python-backend | 80.2 | 86.7 | A- | QUALITY_KEEP | untested | — |
| 468 | `robot-bringup` | testing-qa | 79.5 | 90.0 | A | QUALITY_KEEP | untested | — |
| 469 | `trl-fine-tuning` | ai-ml | 79.5 | 90.0 | A | QUALITY_KEEP | untested | — |
| 470 | `3-statement-model` | finance | 79.4 | 85.6 | A- | QUALITY_KEEP | untested | — |
| 471 | `3d-articulated-print-in-place` | python-backend | 79.4 | 85.6 | A- | QUALITY_KEEP | untested | — |
| 472 | `bug-hunter` | testing-qa | 79.4 | 85.6 | A- | QUALITY_KEEP | untested | — |
| 473 | `react-native-testing` | mobile | 79.4 | 85.6 | A- | QUALITY_KEEP | untested | — |
| 474 | `uniprot-database` | python-backend | 79.4 | 85.6 | A- | QUALITY_KEEP | untested | — |
| 475 | `godot-component-system` | godot-extra | 78.7 | 88.9 | A- | QUALITY_KEEP | untested | — |
| 476 | `3d-image-to-model` | svg-and-design | 78.0 | 87.8 | A- | QUALITY_KEEP | untested | — |
| 477 | `game-godot` | godot | 76.3 | 81.1 | B+ | QUALITY_KEEP | untested | — |
| 478 | `comfyui-workflow-builder` | agent-os | 76.1 | 74.4 | B- | LIBRARY_ONLY | untested | independent LIBRARY_ONLY still present; pending review |
| 479 | `chrome-browser-automation` | agent-os | 74.3 | 63.3 | C | LIBRARY_ONLY | untested | independent LIBRARY_ONLY still present; pending review |
| 480 | `context-optimization` | agent-os | 73.7 | 66.7 | C+ | LIBRARY_ONLY | untested | independent LIBRARY_ONLY still present; pending review |
| 481 | `senior-frontend` | agent-os | 73.0 | 67.8 | C+ | MERGE_OR_DROP | untested | independent MERGE_OR_DROP still present; pending review |
| 482 | `svg-quality-audit` | agent-os | 66.0 | 57.8 | D | DROP | untested | independent DROP still present; pending review |
| 483 | `godot-export-builds` | godot | 65.0 | — | — | — | untested | v1 chair; no independent CSV row |
| 484 | `localimage-stills` | agent-os | 65.0 | — | — | — | untested | v1 chair; no independent CSV row |
| 485 | `quality-at-scale` | agent-os | 65.0 | — | — | — | untested | v1 chair; no independent CSV row |
| 486 | `yt-shorts-flow-director` | agent-os | 65.0 | — | — | — | untested | v1 chair; no independent CSV row |
| 487 | `godot-gdscript-patterns` | godot | 62.8 | 53.3 | D | DROP | untested | independent DROP still present; pending review |
| 488 | `illustration-direction` | agent-os | 62.4 | 65.6 | C+ | MERGE_OR_DROP | untested | independent MERGE_OR_DROP still present; pending review |
| 489 | `inspire-quote-effects` | agent-os | 45.0 | — | — | — | untested | v1 chair; no independent CSV row |
