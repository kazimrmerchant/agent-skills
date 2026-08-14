# Contributing

This repo is a **ratings set**: unique, installable **sibling** folders. PRs that grow file count without a capability story will be declined. Tags are catalog indexes, not parent directories.

## Before you add a skill

1. It is not an exact body-duplicate of something already here.
2. It is not a flood twin (`azure-*`, `performing-*`, `godot-genre-*`, `cursor-rules-for-*`).
3. It is QUALITY_KEEP / Ship A (or a uniquely strong Ship B with real demand).
4. It has no personal paths, emails, vault roots, or live keys.
5. It belongs on [ROADMAP.md](ROADMAP.md) as a **sibling** under `skills/<name>/` with a catalog tag, or it replaces a weak body.

Do not add "just one more KEEP from the private core." Do not nest a skill inside another skill.

## Skill shape

Every skill is a folder:

```
skills/<skill-name>/
  SKILL.md          # required
  reference.md      # optional, loaded on demand
  examples.md       # optional
  scripts/          # optional, reviewed before run
```

`SKILL.md` must have YAML frontmatter:

```yaml
---
name: skill-name
description: What it does and when to use it. Include not-for. Trigger phrases.
---
```

Body should include:

- When to Use / Do not use
- Procedure (numbered)
- Pitfalls
- Verification (falsifiable)

Prefer <500 lines in `SKILL.md`. Push depth to `reference.md`.

## How to propose

1. Fork. One pack or one skill per PR.
2. Copy real files (no junctions, no `node_modules`).
3. Sanitize: home dir → `~/`, vault/drive roots → env or `YOUR_VAULT` / `YOUR_DRIVE`, emails → `user@example.com`.
4. Update [docs/CATALOG.md](docs/CATALOG.md) and [docs/INCLUSION.md](docs/INCLUSION.md) if the v1 set changes.
5. Add upstream links to [NOTICE](NOTICE) when the body is not original.
6. Run the PII greps in the README security section (profile name, mailbox provider addresses, vault roots, live token prefixes).

## What we will not merge

- Bulk rewriter output / shard scripts as the author of skill bodies
- Offensive dual-use / exploit procedures
- "Add my 200 KEEP list"
- Secrets, `.env`, or machine-only Hermes ops

## License

By contributing you agree original text is MIT. Keep upstream licenses visible in the skill header and NOTICE.
