# Inclusion and exclusion

This public tree is **487** unique sibling folders. Tags are catalog indexes, not parent directories. Rate the works-verified subset first: [WORKS_VERIFIED.md](WORKS_VERIFIED.md). This file is include/exclude rules — not a private-library diary.

## Selection algorithm

1. Pool = independent re-assessment `my_decision == QUALITY_KEEP` (927). Prefer score ≥ 90, then remaining A-tier (`A+` / `A` / `A-`). Prior catalog KEEP was overscored — not used as the floor.
2. Skip exact `dir` already in the public tree (the original 51).
3. Hard excludes (0 copies): `godot-genre-*`, `azure-*`, `performing-*`, `cursor-rules-for-*`, wordpress / seismic / pymatgen / book-sft, `web-scraping-anti-bot`, `art-direction-islamic-mv`, `_improved`, junctions, `node_modules`, `LIBRARY_ONLY` / `QUARANTINE` / `MERGE_OR_DROP` / `DROP` / `REWRITE`, example-stubs / templates-quickstart, offensive/exploit/red-team flood.
4. One body per capability (family collapse: angular, gsap, makepad, radix, design-taste-frontend, video-assembly twins, Godot twins of the v1 23, monte-carlo vendor cap 2, …).
5. Assign a **tag** from name + description. CSV `pack` is a weak hint only (that column is polluted). If a skill has no honest tag, skip it — do not dump into `misc/`.
6. Cap tags so the README stays curated (frontend 40, defensive-security 25, devops 30, …).
7. Copy as **real files** (not junctions). Lean: `SKILL.md` plus portable `reference.md` / `examples.md` / `scripts/`. Sanitize profile paths, vault roots, owner emails.
8. **last30days** is not copied. Companion install of upstream v3 only (ratings honesty).

Scripts were used for inventory, selection JSONL, copy, PII rewrite, and catalog **extraction**. Scripts did **not** author SKILL.md bodies.

## Layout

```
skills/<skill-name>/SKILL.md
```

v1 used `skills/agent-os/<skill>` and `skills/godot/<skill>`. Those parents were flattened. Install one sibling folder only.

## Counts

| Bucket | Count |
|--------|------:|
| v1 siblings (flattened) | 51 |
| Expansion (unique A-tier KEEP, after uniqueness cut) | 440 |
| Uniqueness-era total | 491 |
| Cut this wave (claimed files missing) | `automated-email-campaigns`, `shipping-and-launch` |
| **Public total** | **487** |
| last30days | 0 in this repo (upstream companion) |
| Cut after maintainer gate | `yesterdays` (vendored last30days), Whisper twins, `ui-and-controls`, `fp-ts-react`, `mpc-horizon-tuning`, Makepad extras, `media-audio-extraction` |

## Tag caps (expansion)

See [CATALOG.md](CATALOG.md) for tag buckets. Frontend KEEP in the CSV is 144 — we shipped **40 unique** frontend skills. Azure KEEP 26 → **0**. Godot genre → **0**.

## Excluded (never in this public tree)

- Private-library-only skills and flood twins (see hard excludes above)
- `LIBRARY_ONLY`, `QUARANTINE`, `MERGE_OR_DROP`, `DROP`
- Nested mega-packs, `node_modules`, junctions, `_improved`, assessment copies
- Flood families listed above
- `art-direction-islamic-mv` (removed from git 2026-08-14; keep private)
- Stale library `last30days` v1.0.1 (paid-key Reddit/X only)
- A naive “top 500 by old score” list (would have included azure flood, frontend clones, `csharp-godot` twin, `web-scraping-anti-bot`, Godot genre)

## Refresh vs copy

**Read + edited in the public tree** (frontmatter, portable paths, PII, structure): the original v1 chairs listed in the first inclusion note.

**Copied + path/email sanitized only** (bodies not rewritten this wave): the 449 expansion siblings. Do not treat those as “all rewritten.”

## Works-bar (executability)

Uniqueness cuts already happened. This pack is **487 unique sibling chairs** (target was 500; shipped 491 after uniqueness cuts), not a padded 500-count drawer. This bar is **executability**, not another uniqueness pass.

A public skill **stays** if:

1. unique capability owner, **AND**
2. a numbered procedure an agent can execute (real commands / files / APIs), **AND**
3. not a stale encyclopedia / twin / flood, **AND**
4. every claimed `references/` or `scripts/` path exists on disk.

Expansion-wave bodies were copied + path/email sanitized, not all rewritten. **Do not claim 487 all work.** Rate [WORKS_VERIFIED.md](WORKS_VERIFIED.md) first. Prefer documenting a works-verified subset over mass deletion. Do not pad toward 500. Do not vendor `last30days` (upstream companion only).

Cut from git only on a **clear fail**: no procedure, broken invented commands, twin/flood leftover, or claimed `references/` / `scripts/` that do not exist. Philosophy-heavy intros or companions that exist at a different path than claimed stay **WEAK**, not automatic cuts. Do not mass-delete the remaining untested copies.

### Stratified sample (2026-08-14)

N=18. Every listed `skills/<name>/SKILL.md` was Read. agy CLI print-mode smoke on **4** only (`better`, `godot-ui`, `angular-state-management`, `github-actions-advanced`); flags before `-p` (this CLI treats the next argv after `-p` as the prompt). Engine: antigravity. agy returned **ENCYCLOPEDIA** on all four when steps were file/API patterns rather than shell — narrower than this bar. Verdicts in the table are **Read** against commands/files/APIs; agy is noted in evidence.

| Skill | Tag | Verdict | Evidence |
|-------|-----|---------|----------|
| `better` | agent-os | WORKS | Numbered 0–5: name deliverable, baseline, scorecard, project verify, `BETTER:` report. agy: ENCYCLOPEDIA (wanted shell, not agent procedure). |
| `godot-ui` | godot | WORKS | Godot 4.3+ Control/Container/Theme/focus GDScript; `references/layout-and-theming.md` present. agy: ENCYCLOPEDIA (snippets ≠ shell). |
| `angular-state-management` | frontend | WEAK | Numbered Signals/NgRx/ComponentStore with real APIs; snippets miss imports (`computed`, `firstValueFrom`) and inject `HttpClient` then `fetch`. agy: ENCYCLOPEDIA. |
| `glassmorphism` | frontend | WEAK | Copy-paste CSS/SwiftUI/Flutter/RN/Compose. Labels itself a child of `design-it` (not in this tree) and “not meant to be triggered directly.” |
| `emil-design-eng` | frontend | WORKS | Numbered animation framework plus real CSS/JS (`cubic-bezier`, `@starting-style`, `clip-path`, WAAPI). Philosophy intro, then executable craft. |
| `stable-diffusion` | ai-ml | WORKS | Numbered HuggingFace Diffusers pipelines (txt2img, img2img, ControlNet, LoRA). `runwayml/stable-diffusion-inpainting` may be stale; not a missing procedure. |
| `energy-procurement` | ai-ml | WEAK | Numbered C&I playbook (load factor, RFP, PPA, demand ratchet) with real formulas; companions exist. Domain playbook, not shell. |
| `weights-and-biases` | ai-ml | WORKS | Numbered `wandb.init` / `log` / `sweep` / artifacts; `wandb login`. Placeholder `YOUR_KEY`. Refs present. |
| `github-actions-advanced` | git-github | WORKS | Step 1 PowerShell discovery; steps 2–5 SHA-pinned workflow YAML an agent writes. agy: ENCYCLOPEDIA. Verify mentions `ConvertFrom-Yaml` (not built-in). |
| `automated-email-campaigns` | devops | FAIL, cut | Numbered lifecycle phases + `Resolve-DnsName` SPF/DKIM/DMARC. Claims `references/*` and `scripts/` that **do not exist**. Folder removed. |
| `shipping-and-launch` | devops | FAIL, cut | Numbered checklist, flag rollout, `npm audit` / health GET. Claims three `references/*` files that **do not exist**. Folder removed. |
| `shadcn` | frontend | WEAK | Numbered `npx shadcn@latest` CLI. SKILL.md points at `references/cli.md`; files sit at skill root (`cli.md`, `customization.md`). |
| `git-workflow` | agent-os | WORKS | Numbered PowerShell git/gh; `safety-checklist.md`, `reference.md`, `examples.md` present. |
| `docker-management` | devops | WORKS | Numbered `docker` / `compose` lifecycle; PowerShell `${PWD}` notes. |
| `ollama-local-setup` | ai-ml | WORKS | Numbered Windows `setx` / junction / kill-one-server; `scripts/verify-ollama.ps1` exists. |
| `threejs-fundamentals` | threejs | WORKS | Numbered scene/camera/renderer/Object3D with real THREE APIs. Later math dump is reference; first five scaffold. |
| `chroma` | ai-ml | WORKS | Numbered chromadb `create_collection` / `add` / `query`. Star-count padding is fluff; API is real. |
| `mcp-server-authoring` | agent-os | WORKS | Numbered transport/language/tool-design; `npx @modelcontextprotocol/inspector`; templates present. |

**This sample:** N=18 · WORKS 12 · WEAK 4 · FAIL 2 · ENCYCLOPEDIA 0 (Read). agy 4/4 ENCYCLOPEDIA on a shell-only reading. **Cuts this wave:** `automated-email-campaigns`, `shipping-and-launch` (claimed companion files missing). Remaining **471** untested copies (487−16). This sample does not verify the tree.
