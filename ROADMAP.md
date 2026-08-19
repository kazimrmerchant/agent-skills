# ROADMAP

Rate the **works-verified** subset first: [docs/WORKS_VERIFIED.md](docs/WORKS_VERIFIED.md) (12 chairs). The uniqueness inventory is **496** sibling skills with catalog tags (target **500** unique that meet the gold bar — not a pad). `llama-cpp` plus seven unique A-tier chairs added 2026-08-14. Original `awe-me` added 2026-08-18 (canonical [kazimrmerchant/awe-me](https://github.com/kazimrmerchant/awe-me); not padded onto the 12).

Do not interpret this file as permission to `git add` the rest of a 5k library.

## Now in-tree (tags, not folders)

agent-os, godot, frontend, threejs, video, research, defensive-security, godot-extra, mcp-and-tools, python-backend, data-databases, ai-ml, git-github, testing-qa, devops, automation, game-dev, mobile, and the smaller named tags in the README.

## Still private / companion

| Item | Why |
|------|-----|
| `last30days` | Upstream [mvanhorn/last30days-skill](https://github.com/mvanhorn/last30days-skill) v3. Install with `npx skills add`. Not vendored here. |
| `art-direction-islamic-mv` | Private forever |
| Remaining untagged A-tier KEEP (~160) | No honest tag without a `misc/` dump; do not pad |
| B-tier KEEP | Quality floor is A-tier for this wave |
| Azure / genre / performing / cursor-rules-for / wordpress / seismic / pymatgen / book-sft | Flood / twins |
| LIBRARY_ONLY / QUARANTINE | Never |

## Gates for any later sibling

1. Flat `skills/<name>/SKILL.md` (no pack parent, no nested SKILL.md).
2. Catalog what/how row (extracted, not a stamped essay).
3. PII scan PASS.
4. NOTICE updated for provenance.
5. Exclude LIBRARY_ONLY, QUARANTINE, MERGE_OR_DROP losers, blocked-PII until rebuilt.

## Explicitly never

- wordpress / seismic / pymatgen / book-sft twins from `publish/core`
- Pushing the dirty local `publish/` tree (junctions, Playwright `node_modules`)
- Publishing all unique `SKILL.md` files because a database said QUALITY_KEEP
- Nesting last30days (or any skill) inside another skill body
