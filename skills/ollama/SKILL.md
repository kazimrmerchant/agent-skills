---
name: ollama
description: >-
  Run Ollama Cloud API with glm-5.2 at high reasoning, then execute the returned
  instructions. Use when the user types /ollama, says ollama cloud, cloud glm,
  or asks to plan/execute via Ollama Cloud GLM 5.2.
disable-model-invocation: true
---

# /ollama — Ollama Cloud GLM 5.2

## Purpose

When the user runs **`/ollama <task>`**:

1. Call **Ollama Cloud API** with **`glm-5.2`** and **`reasoning_effort: high`**
2. Treat GLM’s reply as the **instruction set**
3. **Execute** those instructions in Cursor (files, shell, verify)
4. Report what was done + evidence

This is **cloud API**, not local GPU. Local models stay on `/local`.

## Standing approval

`/ollama` is standing approval to spend Ollama Cloud usage for that turn (default one planner call). Ask again only if you need **extra** paid providers, large multi-call fan-out, or a different heavy model.

## API contract

| Setting | Value |
|--------|--------|
| OpenAI base | `https://ollama.com/v1` |
| Native host | `https://ollama.com` |
| Auth | `Authorization: Bearer $OLLAMA_API_KEY` |
| Key | environment `OLLAMA_API_KEY` (never print it) |
| Model | `glm-5.2` (no `:cloud` suffix) |
| Reasoning | `high` |
| max_tokens | **omit** unless user caps |

## Preferred call (OpenAI-compatible)

```powershell
curl.exe -sS https://ollama.com/v1/chat/completions `
  -H "Authorization: Bearer $env:OLLAMA_API_KEY" `
  -H "Content-Type: application/json" `
  -d '{"model":"glm-5.2","reasoning_effort":"high","messages":[{"role":"user","content":"<TASK>"}]}'
```

If the user has a local helper script that wraps this API, use that. Do not require a machine-specific runtime path.

## Workflow

1. **Parse task** — text after `/ollama`. If missing, ask once.
2. **Gather minimal context** — only paths/files needed so GLM can instruct well (prefer paths over huge dumps).
3. **Call API** via the helper above. Do not print the API key. Do not use Cloudflare GLM MCP as a substitute for `/ollama`.
4. **Execute** — follow GLM’s steps; adapt only for safety/project rules/Windows PowerShell realities.
5. **Verify** — run the project’s checks or the verification GLM specified.
6. **Report** — short outcome: what GLM decided, what you did, verification result, blockers.

## Modes

| User says | Behavior |
|-----------|----------|
| `/ollama …` (default) | Plan via API **then execute** |
| `/ollama plan …` or “plan only” | Call API, present plan, **stop** |
| `/ollama <other-model> …` | Use named cloud model id if listed by `… list`; else default `glm-5.2` |

## Anti-patterns

- Using `http://localhost:11434` for `/ollama`
- Using `ollama run glm-5.2:cloud` instead of the API key path
- Substituting Cloudflare `ask_glm_5_2` when the user asked for `/ollama`
- Embedding or echoing `OLLAMA_API_KEY`
- Passing `max_tokens` by default
- Stopping after the plan when the user wanted the job done

## Related

- Local GPU opt-in: `/local` (separate rule)
- Official API: https://ollama.com/v1
