# Agent Skills

**Named packs** of Agent Skills other AIs can install and rate.

v1 is two packs:

1. **`godot`** — curated Godot 4 slices (first public target; already scanned clean)
2. **`agent-os`** — 28 unique chairs (`better`, `goal`, `review-results`, …)

This is **not** a 250-file score drawer and **not** a 5,000-skill vault dump. Ratings beat raw count. Later Ship A skills are listed in [ROADMAP.md](ROADMAP.md), not stuffed into v1.

Layout follows the portable skill folder convention used by Cursor, Claude, and [agentskills.io](https://agentskills.io): one directory per skill, `SKILL.md` at the root of that directory.

```
skills/
  agent-os/<skill>/SKILL.md
  godot/<skill>/SKILL.md
```

## What this is

- Progressive-disclosure skills: `name` + `description` are the trigger; the body loads when the task matches.
- A quality bar: uniqueness, actionability, no exact-dup losers, no flood families.
- MIT for original docs and original chairs. **Not every body is original** — see [NOTICE](NOTICE).

## What is not included

- Private library-only skills
- Quarantine / dual-use offensive content
- Exact body-duplicate losers
- Personal vault paths, live keys, owner emails
- Nested mega-packs, `node_modules`, junctions
- The rest of unique Ship A (frontend flood, video twins, research extras) — [ROADMAP.md](ROADMAP.md)

Full include/exclude: [docs/INCLUSION.md](docs/INCLUSION.md). Catalog of v1 only: [docs/CATALOG.md](docs/CATALOG.md).

## Install

Clone this repository, then copy **only the pack you need** into the host agent's skills directory. Do not load both packs plus a private 5k library into the always-on set.

### Cursor

Personal (all projects):

```powershell
git clone https://github.com/kazimrmerchant/agent-skills.git agent-skills
Copy-Item -Recurse .\agent-skills\skills\godot\* $env:USERPROFILE\.cursor\skills\
# or the agent-os chairs:
Copy-Item -Recurse .\agent-skills\skills\agent-os\* $env:USERPROFILE\.cursor\skills\
```

Project-local (this repo only):

```
.cursor/skills/<skill-name>/SKILL.md
```

Never install into `~/.cursor/skills-cursor/` (Cursor-managed built-ins).

### Claude

```powershell
Copy-Item -Recurse .\agent-skills\skills\godot\* $env:USERPROFILE\.claude\skills\
```

### Hermes / other agentskills.io hosts

Copy the same folders into that product's skills root (often `~/.hermes/skills` or a project `skills/` directory). One folder per skill; `SKILL.md` required.

### Lean load

| Goal | Install |
|------|---------|
| Godot 4 game work | `skills/godot/` only |
| Agent operating system (goal/better/review/git) | `skills/agent-os/` subset |
| Shorts / local video | `yt-shorts-flow-director`, `localvideo`, `localimage-stills`, `local-media-router`, `review-results` |
| Frontend only | `senior-frontend`, `web-interface-guidelines` |

An agent should **Read** one matching `SKILL.md`, not the whole pack.

## Quality bar

Scored for public Git (see [docs/VALUE_FRAMEWORK.md](docs/VALUE_FRAMEWORK.md)):

| Dimension | Meaning |
|-----------|---------|
| Clarity | `name` + `description` fire correctly; when / not-for |
| Actionability | Numbered procedure, commands, pitfalls |
| Completeness | Enough to execute; depth in `references/` |
| Uniqueness | One capability owner |
| Demand | Real workflows, not genre spam |
| Safe-to-publish | No exploits, secrets, or personal paths |
| Maint | Stable enough to update |

v1 chairs were read and sanitized. Remaining body upgrades continue in waves — this commit does **not** claim all 51 were rewritten from scratch.

## Security

- Review bundled scripts before running them.
- Do not paste API keys into chat or commit `.env`.
- `chrome-browser-automation` and `grokimagine` drive a **real** Chrome over CDP. They are not anti-bot / CAPTCHA tools.

## License

[MIT](LICENSE) for original material. Upstream Godot / community skills: [NOTICE](NOTICE).

## Contributing

[CONTRIBUTING.md](CONTRIBUTING.md). New skills belong on the roadmap as a **named pack**, not a root-level dump.
