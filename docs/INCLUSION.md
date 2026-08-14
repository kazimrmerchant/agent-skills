# Inclusion and exclusion

**Lock (2026-08-14, revised):** unique **sibling** skills other AIs can copy one-folder-at-a-time and rate. Target was 500; shipped **491** after uniqueness cuts (quality over the integer). Tags are catalog indexes, not parent folders. Not a 250-file score junk drawer. Not 5,945 files.

Authoritative private indexes (not in this repo): Skills-Catalog `10-OSS-READINESS.md` and `assessment/assessed-skills.csv`.

## Selection algorithm

1. Pool = independent re-assessment `my_decision == QUALITY_KEEP` (927). Prefer score ≥ 90, then remaining A-tier (`A+` / `A` / `A-`). Prior catalog KEEP was overscored — not used as the floor.
2. Skip exact `dir` already in the public tree (the original 51).
3. Hard excludes (0 copies): `godot-genre-*`, `azure-*`, `performing-*`, `cursor-rules-for-*`, wordpress / seismic / pymatgen / book-sft, `web-scraping-anti-bot`, `art-direction-islamic-mv`, `_improved`, junctions, `node_modules`, `LIBRARY_ONLY` / `QUARANTINE` / `MERGE_OR_DROP` / `DROP` / `REWRITE`, example-stubs / templates-quickstart, offensive/exploit/red-team flood.
4. One body per capability (family collapse: angular, gsap, makepad, radix, design-taste-frontend, video-assembly twins, Godot twins of the v1 23, monte-carlo vendor cap 2, …).
5. Assign a **tag** from name + description. CSV `pack` is a weak hint only (that column is polluted). If a skill has no honest tag, skip it — do not dump into `misc/`.
6. Cap tags so the README stays curated (frontend 40, defensive-security 25, devops 30, …).
7. Copy from `library/<dir>/` as **real files** (not junctions). Lean: `SKILL.md` plus portable `reference.md` / `examples.md` / `scripts/`. Sanitize profile paths, vault roots, owner emails.
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
| **Public total** | **491** |
| last30days | 0 in this repo (upstream companion) |
| Cut after Hadi/Maryam | `yesterdays` (vendored last30days), Whisper twins, `ui-and-controls`, `fp-ts-react`, `mpc-horizon-tuning`, Makepad extras, `media-audio-extraction` |

## Tag caps (expansion)

See root README tag table. Frontend KEEP in the CSV is 144 — we shipped **40 unique** frontend chairs. Azure KEEP 26 → **0**. Godot genre → **0**.

## Excluded (never in this public tree)

- The ~220–250 `publish/core` KEEP drawer (wordpress / seismic / pymatgen / book-sft twins)
- All ~5,945 unique private `SKILL.md` files
- `LIBRARY_ONLY`, `QUARANTINE`, `MERGE_OR_DROP`, `DROP`
- Nested mega-packs, `node_modules`, junctions, `_improved`, assessment copies
- Flood families listed above
- `art-direction-islamic-mv` (removed from git 2026-08-14; keep private)
- Stale library `last30days` v1.0.1 (paid-key Reddit/X only)
- A naive “top 500 by old score” list (would have included azure flood, frontend clones, `csharp-godot` twin, `web-scraping-anti-bot`, Godot genre)

## Refresh vs copy

**Read + edited in the public tree** (frontmatter, portable paths, PII, structure): the original v1 chairs listed in the first inclusion note.

**Copied + path/email sanitized only** (bodies not rewritten this wave): the 449 expansion siblings. Do not treat those as “all rewritten.”
