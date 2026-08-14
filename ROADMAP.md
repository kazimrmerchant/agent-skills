# ROADMAP — later named packs

v1 shipped **godot** + **agent-os** only. Unique Ship A that is still private stays here as a queue, not a dump.

Do not interpret this file as permission to `git add` the rest of a 5k library.

## Pack candidates (after v1 is live and clean)

| Pack | Intent | Likely keep (illustrative, not a dump list) | Hold back |
|------|--------|---------------------------------------------|-----------|
| `frontend` | React/Next/a11y/CSS that is unique | shadcn discovery, semantic layout, responsive — **one** body per capability | 532-wide frontend flood, cursor-rules-for-* |
| `threejs` | Specialists the `threejs-skill-router` points at | camera, materials, atmosphere, shadows — unique KEEP only | Nested mega-packs, image-generator spam |
| `video` | Remotion / ffmpeg / story-to-video | remotion-shorts, ffmpeg-video-editing, story-to-video | Flow/Veo machine-ops until sanitized; genre twins |
| `research` | Methods beyond `end-to-end-research` | research-report-synthesis, scientific-writing (if unique) | book-sft / paper-mill twins |
| `defensive-security` | Audit / threat-model / hardening | 15–25 defensive KEEP | `performing-*`, red-team flood, exploit payloads |
| `godot-extra` | Optional systems not in the v1 23 | input, particles, shaders-basics, multiplayer, testing-patterns | `godot-genre-*` (27), adapt-*, platform-* |
| `mcp-and-tools` | Tool design beyond `mcp-server-authoring` | tool-design, skill-creator | Nested skill-catalog dumps |

## Gates for any later pack

1. Named pack folder + catalog page (not a flat 200-file drop).
2. PII scan PASS on the new tree.
3. NOTICE updated for provenance.
4. Each added skill has trigger-quality frontmatter. Body upgrades may continue in waves — do not fake "all rewritten."
5. Exclude LIBRARY_ONLY, QUARANTINE, MERGE_OR_DROP losers, blocked-PII until rebuilt.

## Explicitly never

- wordpress / seismic / pymatgen / book-sft twins from `publish/core`
- Pushing the dirty local `publish/` tree (junctions, Playwright `node_modules`)
- Publishing all unique `SKILL.md` files because a database said QUALITY_KEEP
