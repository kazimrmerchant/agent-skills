---
name: context-optimization
version: 1.1.1
description: "Use when improving context efficiency: context budgeting, observation masking, prefix or KV-cache strategy, partitioning, token-cost reduction, retrieval scoping, or extending effective context capacity without lowering answer quality. Load when tool outputs dominate trajectories, cache hit rates are poor, or multi-step sessions approach window limits. Not for diagnosing degradation (context-degradation), designing lossy handoff summaries (context-compression), or explaining attention fundamentals. Action: apply lossless tactics first (KV-cache, masking, scoping), then compaction and partitioning with measured gates. Exception: skip on short low-pressure chats and maximum-fidelity / audit workloads."
risk: safe
source: openrouter-deepsearch
date_added: 2026-06-16
---

# Context Optimization Techniques

Context optimization extends the effective capacity of a finite context window through **caching, masking, budgeting, compaction, retrieval scoping, and partitioning**. The goal is not fewer tokens for their own sake — it is to keep the *signal* the model needs while shedding the *noise* it does not.

That distinction is load-bearing. An optimization that halves token count but drops a user constraint, an error stack, or a file path is a **regression**. Every technique in this skill is therefore paired with a reason, a default threshold, and a measurement. Prefer numbers over reputation.

| Goal | What "good" looks like |
|------|------------------------|
| Capacity | Task finishes without overflow or mid-turn truncation |
| Cost | Lower billed input tokens *and* cheaper cache hits where supported |
| Latency | Faster TTFT / end-to-end when prefixes stay cacheable |
| Quality | Task success and probe scores stay within an agreed drop budget (e.g. ≤5%) |

**Runnable utilities live in this skill:**

| Path | Role | When to load |
|------|------|--------------|
| `scripts/compaction.py` | Token estimates, category-aware summarization, `ObservationStore`, `ContextBudget`, cache-stable prompts, cache metrics | Load when implementing or adapting any masking, budgeting, compaction, or cache-stability logic. Run `python scripts/compaction.py` from the skill root to verify all paths work. |
| `references/optimization_techniques.md` | Deeper reference on thresholds, patterns, pitfalls, monitoring, and integration sketches | Load when you need selective masking patterns, partitioning sketches, monitoring alerts, integration patterns, or performance targets beyond the core procedure below. |

---

## When to Use

Activate this skill when any of the following apply:

| Signal | Why optimization is the right tool |
|--------|------------------------------------|
| Context budgets or token costs constrain task complexity | Work no longer fits without reducing noise or splitting work |
| Tool / observation output dominates the trajectory | Masking usually yields the largest capacity gain |
| Prefix or KV-cache hit rate is low and stable prefixes exist | Cache-friendly ordering is near-zero quality risk |
| Retrieval loads many low-relevance documents | Scoping frees budget for the live task |
| One window cannot hold the full problem | Partition across sub-agents with isolated contexts |
| You need automatic budget triggers | Fire masking / compaction / partitioning by policy, not guesswork |
| High-throughput / low-latency apps | Token efficiency is a first-order cost and latency driver |
| Long multi-step agent sessions | Accumulated history and tool dumps would otherwise overflow |

### Typical triggers in practice

- Utilization crosses ~**70%** (warning / plan compaction) or ~**80%** (act; see `ContextBudget.should_optimize` in `scripts/compaction.py`).
- Tool-output category alone exceeds its allocation (often ~30–35% of the window).
- Inference metrics show KV-cache miss rate **> ~40%** with a non-trivial prefix length.
- Retrieval logs show irrelevant-hit ratio **> ~20%** over a recent window of queries.
- Estimated full-task context **> ~60%** of the limit *and* the work decomposes into ≥3 independent subtasks.

### Do NOT use — route to a different skill

| Problem | Correct skill | Why not this skill |
|---------|---------------|--------------------|
| Explaining *why* attention / windows behave a certain way | `context-fundamentals` | Mental model, not token tactics |
| Active lost-in-middle, poisoning, distraction, confusion, or clash | `context-degradation` | Diagnose quality failure first; optimizing a polluted window hides the bug |
| Structured lossy handoff summaries, multi-cycle compression quality | `context-compression` | Compression owns summary schemas, probes, and drift control |
| Storing large outputs, plans, or logs as files | `filesystem-context` / scratchpad patterns | Verbatim offload beats lossy shrink when re-fetch is cheap |
| Cross-session semantic memory | memory systems | Optimization is within-session efficiency |

### Skip optimization entirely when

- **Short, low-pressure conversations** — machinery costs tokens and latency; below the pressure point it is net-negative.
- **Maximum-fidelity tasks** (compliance, audit, legal discovery) — compaction and masking are inherently lossy; offload full records to files.
- **Runtimes without prefix / KV-cache support** — cache-friendly ordering buys nothing until the engine (vLLM, TGI, TensorRT-LLM, provider prompt-cache APIs) actually reuses prefixes.
- **Security-sensitive masking without a trust boundary** — the reference store is part of the attack surface; control access and do not leak via metadata or access patterns.
- **The real problem is noise, not size** — if distractors or contradictions dominate, run `context-degradation` before shrinking.

---

## Prerequisites

- **Runtime with prefix/KV-cache support** (vLLM, TGI, TensorRT-LLM, OpenAI prompt caching, Anthropic prompt caching) — required for Step 2 only; other steps work on any LLM runtime.
- **Python 3.10+** for running `scripts/compaction.py` utilities.
- **Model-specific tokenizer** (e.g. `tiktoken` for OpenAI, Anthropic tokenizer, HuggingFace tokenizers for local models) for production token counting. The skill's heuristic (`~4 chars/token` for English) is for hot-path budgeting only, not billing truth.
- **Windows host (PowerShell)**: All commands below assume PowerShell. Use `python scripts\compaction.py` (backslash) on Windows if forward slashes fail. Paths like `~\.cursor\skills\context-optimization\` are the install location.

---

## Procedure

### Core concepts: signal vs noise

| Keep (signal) | Shed or mask (noise) |
|---------------|----------------------|
| System / policy instructions | Resolved tool dumps already acted on |
| User constraints and goals | Boilerplate, repeated exploration chatter |
| Active error stacks while debugging | Old intermediate reasoning once decisions are logged |
| Tool / function schemas | Superseded retrieval chunks |
| Recent turn driving the next action | Full documents after key facts are extracted |
| Decisions, file paths, IDs, open questions | Duplicate copies of the same content |

### Lossless-first priority order

Apply strategies in this order so lossy steps operate on a smaller, cleaner window:

1. **KV-cache / prefix stability** — lossless when the runtime supports it; free fidelity.
2. **Observation masking** — near-lossless *if* originals stay retrievable.
3. **Retrieval scoping** — prevent irrelevant bulk from entering the window.
4. **Budget policy + triggers** — automatic, consistent decisions under load.
5. **Compaction / summarization** — lossy; preserve anchors; degrade gracefully on failure.
6. **Context partitioning** — coordination overhead; use when one window cannot hold the problem.

Governing principle: **quality of context over quantity of tokens**. Measure baseline → apply one change → measure again → keep only if gates pass.

### Step 0 — Baseline before changing anything

1. Log **per-turn** token counts by category (system, tools, history, tool outputs, retrieved docs).
2. Log **task success** (or quality score) alongside utilization.
3. Log **latency** and, if available, **KV-cache hit rate**.
4. Identify the dominant category. Strategy choice follows composition (see Step 1).

Without a baseline you cannot tell whether an optimization helped or merely added overhead.

### Step 1 — Choose tactics from composition

| If this dominates | Apply first | Then |
|-------------------|-------------|------|
| Tool / observation outputs | Observation masking (`ObservationStore`) | Compaction of history |
| Stable system + tools + templates | KV-cache prefix design | Masking / compaction |
| Retrieved documents | Retrieval scoping + doc summaries | Partition research vs act |
| Message history | Compaction at ~70–80% utilization | Sliding window of recent turns |
| Multiple independent subtasks | Partitioning | Aggregate structured results only |
| Mixed pressure | Budget policy with prioritized triggers | Incremental rollout |

> **Load `references/optimization_techniques.md`** when you need deeper patterns, integration sketches, or threshold tuning beyond this table.

### Step 2 — KV-cache / prefix optimization (do this first when applicable)

**Why:** A single byte change early in the prompt invalidates every cached block after it. Stable prefixes turn repeated system/tool text into cache hits → lower cost and latency with **no** fidelity loss.

**Rules:**

1. Order **most-stable → most-volatile**: system prompt → tool definitions → reusable templates / few-shots → history → current user turn.
2. Keep the stable prefix **byte-identical** across requests (same whitespace, same tool list order, same serialization).
3. Move timestamps, request IDs, session IDs, and counters **out of** the system prefix into trailing messages or tool args.
4. Confirm the runtime actually implements prefix/KV reuse before investing.

**Stabilize dynamic text with the skill script:**

```python
from scripts.compaction import design_stable_prompt, calculate_cache_metrics

raw = "Session 42 started on 2025-12-20. Progress: 3/10 tasks."
stable = design_stable_prompt(raw)
# → dynamic dates / session / counters replaced with stable placeholders

# After collecting request logs with prefix_hash + token_count:
metrics = calculate_cache_metrics(requests, cache_state)
# metrics["hit_rate"], metrics["recommendations"]
```

**Cache-friendly assembly pattern:**

```python
from __future__ import annotations

from dataclasses import dataclass
from enum import Enum
from typing import Mapping, Sequence


class Role(str, Enum):
    SYSTEM = "system"
    USER = "user"
    ASSISTANT = "assistant"


@dataclass(frozen=True)
class ChatMessage:
    role: Role
    content: str


@dataclass(frozen=True)
class ToolDefinition:
    name: str
    description: str
    parameters: Mapping[str, str]


_DYNAMIC_MARKERS: tuple[str, ...] = ("{timestamp}", "{request_id}", "{session_id}")


def render_tool_definitions(tools: Sequence[ToolDefinition]) -> str:
    if not tools:
        raise ValueError("at least one tool definition is required")
    lines = [
        f"- {tool.name}: {tool.description} (params: {', '.join(sorted(tool.parameters))})"
        for tool in tools
    ]
    return "Available tools:\n" + "\n".join(lines)


def construct_cache_friendly_context(
    system_prompt: str,
    tools: Sequence[ToolDefinition],
    reusable_templates: Sequence[str],
    chat_history: Sequence[ChatMessage],
    user_input: str,
) -> list[ChatMessage]:
    """Order segments most-stable first so the prefix KV-cache stays warm."""
    if not system_prompt.strip():
        raise ValueError("system_prompt must not be blank")
    if not user_input.strip():
        raise ValueError("user_input must not be blank")
    for marker in _DYNAMIC_MARKERS:
        if marker in system_prompt:
            raise ValueError(
                f"system_prompt contains dynamic marker {marker!r}; move it to a "
                "trailing message so the cached prefix stays byte-stable"
            )

    context: list[ChatMessage] = [
        ChatMessage(Role.SYSTEM, system_prompt),
        ChatMessage(Role.SYSTEM, render_tool_definitions(tools)),
    ]
    context.extend(ChatMessage(Role.SYSTEM, template) for template in reusable_templates)
    context.extend(chat_history)
    context.append(ChatMessage(Role.USER, user_input))
    return context
```

**Target benchmarks** (from reference; calibrate on your stack):

- ≥70% cache hit rate on stable workloads
- Material cost and TTFT reduction when hits land

### Step 3 — Observation masking (largest capacity win for agents)

**Why:** In tool-heavy agents, raw observations (search dumps, file contents, API JSON, logs) often dominate the trajectory. Once the model has used a result, the full payload rarely needs to stay inline every turn.

**Contract (non-negotiable):**

1. Store the full observation under a **stable reference id**.
2. Replace the body with a short placeholder + key point + retrieval instruction.
3. Expose a **retrieve** path the agent can call on demand.
4. **Do not mask** active errors / stack traces while debugging is ongoing.

**Use the skill implementation:**

```python
from scripts.compaction import ObservationStore

store = ObservationStore(max_size=1000)
masked, ref_id = store.mask(long_tool_output, max_length=200)
# masked: "[Obs:a1b2c3d4 elided. Key: ... Full content retrievable.]"
# ref_id is None when content was already short enough

if ref_id is not None:
    original = store.retrieve(ref_id)
```

**Demo from skill root (PowerShell):**

```powershell
python scripts\compaction.py
```

**Selective policy** (mask only when safe):

| Keep full text | Mask / reference |
|----------------|------------------|
| Current turn tool result still under active reasoning | Older resolved tool dumps |
| Errors while `is_active_error` / debugging | Successful large payloads already summarized |
| High relevance to open task | Low relevance and age > N turns |
| Small outputs under threshold (e.g. 2k chars) | Large dumps above threshold |

**Typed pattern (reference store + deterministic key):**

```python
from __future__ import annotations

import re
import uuid
from dataclasses import dataclass
from typing import Final

_MAX_OBSERVATION_CHARS: Final[int] = 2_000
_KEY_SENTENCES: Final[int] = 2


@dataclass(frozen=True)
class Observation:
    tool_name: str
    content: str
    result_summary: str = ""
    error: str = ""

    def __post_init__(self) -> None:
        if not self.tool_name.strip():
            raise ValueError("Observation.tool_name must be non-empty")
        if not self.content.strip():
            raise ValueError("Observation.content must be non-empty")


class ReferenceStore:
    """In-memory store; swap dict for Redis/DB in production, keep this interface."""

    def __init__(self) -> None:
        self._items: dict[str, Observation] = {}

    def store(self, observation: Observation) -> str:
        ref_id = uuid.uuid4().hex[:12]
        self._items[ref_id] = observation
        return ref_id

    def retrieve(self, ref_id: str) -> Observation:
        try:
            return self._items[ref_id]
        except KeyError as exc:
            raise KeyError(f"unknown observation ref: {ref_id!r}") from exc


def extract_key(observation: Observation, *, max_sentences: int = _KEY_SENTENCES) -> str:
    """Deterministic key so masking adds no model latency per turn."""
    if max_sentences <= 0:
        raise ValueError("max_sentences must be positive")
    if observation.error:
        return f"{observation.tool_name} error: {observation.error[:120]}"
    if observation.result_summary:
        return f"{observation.tool_name}: {observation.result_summary[:120]}"
    normalized = " ".join(observation.content.split())
    sentences = re.split(r"(?<=[.!?])\s+", normalized)
    return " ".join(sentences[:max_sentences]).strip() or normalized[:200]


@dataclass(frozen=True)
class MaskResult:
    text: str
    masked: bool
    ref_id: str | None = None


def process_observation(
    observation: Observation,
    store: ReferenceStore,
    *,
    max_chars: int = _MAX_OBSERVATION_CHARS,
    is_active_error: bool = False,
) -> MaskResult:
    if max_chars <= 0:
        raise ValueError("max_chars must be positive")
    if is_active_error or len(observation.content) <= max_chars:
        return MaskResult(text=observation.content, masked=False)

    ref_id = store.store(observation)
    key = extract_key(observation)
    placeholder = (
        f"[Obs:{ref_id} elided. Key: {key}. Retrieve with get_ref('{ref_id}').]"
    )
    return MaskResult(text=placeholder, masked=True, ref_id=ref_id)
```

**Target benchmarks:**

- 60–80% reduction on masked observations
- ≤~2% quality impact when retrieve path is exercised
- Near-zero added latency if keys are deterministic (not LLM-generated)

**Security:** Treat the store as a trust boundary. Access-control refs; TTL/eviction policy; no sensitive content in the *placeholder* beyond what the agent already saw.

### Step 4 — Retrieval scoping

**Why:** Irrelevant documents tax attention even when "the answer is also in there." Prefer not loading junk over compacting it later.

**Procedure:**

1. Score candidates against the **current** subtask (not the whole session).
2. Cap `top_k` and max tokens per doc; drop below a relevance threshold.
3. Prefer **just-in-time** tool retrieval over preloading "everything that might help."
4. After use, keep a short extract or mask the full doc (same pattern as observations).
5. If multi-hop research is large, partition: researcher agent returns structured notes only.

**Tighten scope when:**

- `irrelevant_hits_ratio > ~0.20` over the last N retrievals
- Retrieved-docs category exceeds its budget allocation
- Quality drops after a retrieval spike (distraction pattern → also check `context-degradation`)

Category-aware document compaction helper:

```python
from scripts.compaction import summarize_content, categorize_messages

# Heuristic lead-paragraph summary for retrieved docs
brief = summarize_content(doc_text, "retrieved_document", max_length=500)

# Or categorize a full message list before selective compaction
cats = categorize_messages(messages)
# cats: system_prompt, tool_definition, tool_output, conversation, retrieved_document, other
```

### Step 5 — Context budgeting and automatic triggers

**Why:** Manual "maybe we should compact" fails under load. Declarative budgets separate *what* (allocations, thresholds) from *how* (mask / compact / restructure).

**Default allocation sketch** (must sum to 1.0; tune to your agent):

| Category | Typical share | Notes |
|----------|---------------|-------|
| Tool outputs | ~0.35 | Often the largest consumer in agents |
| Message history | ~0.30 | Grows with conversation |
| Retrieved documents | ~0.20 | Cap hard or scope aggressively |
| Reserved buffer | ~0.15 | Current turn + system headroom |

**Use `ContextBudget` from the skill:**

```python
from scripts.compaction import ContextBudget

budget = ContextBudget(total_limit=128_000)
budget.allocate("system_prompt", 1_500)
budget.allocate("tool_definitions", 3_000)
budget.allocate("message_history", 40_000)
budget.allocate("tool_outputs", 45_000)

usage = budget.get_usage()
# utilization_ratio, remaining, by_category

should_act, reasons = budget.should_optimize(
    current_usage=int(128_000 * 0.85),
    metrics={"quality_score": 0.92},  # optional degradation signals
)
# should_act True when utilization > 0.8 (or quality/attention metrics trip)
```

**Declarative policy example:**

```yaml
apiVersion: context-optimization.openrouter.ai/v1beta1
kind: ContextPolicy
metadata:
  name: default-agent-policy
  description: "Standard context management policy for general-purpose agents."
spec:
  context_window_limit: 128000
  token_budget_allocations:           # must sum to 1.0
    tool_outputs: 0.35
    message_history: 0.30
    retrieved_documents: 0.20
    reserved_buffer: 0.15
  optimization_triggers:
    - name: kv_cache_miss_rate_high
      condition: "inference_metrics.cache_miss_rate > 0.40"
      action: stabilize_prefix_order
    - name: tool_output_budget_exceeded
      condition: "category_usage.tool_outputs > allocation.tool_outputs"
      action: mask_oldest_resolved_observations
    - name: utilization_critical
      condition: "utilization_ratio > 0.80"
      action: compact_message_history
    - name: retrieval_noise_high
      condition: "retrieval_metrics.irrelevant_hits_ratio > 0.20"
      action: tighten_retrieval_scope
    - name: quality_drop
      condition: "quality_score < 0.90"
      action: reduce_aggression
  preservation_rules:
    - "never_mask_active_errors"
    - "preserve_system_prompt"
    - "preserve_latest_turn"
    - "preserve_file_paths_and_ids"
```

### Step 6 — Compaction / summarization (lossy; gate carefully)

**Why:** When lossless tactics are exhausted, summarize older history while preserving anchors (decisions, file paths, IDs, open questions).

**Procedure:**

1. Categorize messages with `categorize_messages(messages)`.
2. Preserve system prompt, tool definitions, and latest turn verbatim.
3. Summarize older conversation and resolved tool outputs with `summarize_content`.
4. Keep a structured summary with anchors: decisions made, file paths referenced, IDs assigned, open questions.
5. Measure quality before and after; roll back if quality drop exceeds budget.

```python
from scripts.compaction import categorize_messages, summarize_content, ContextBudget

cats = categorize_messages(messages)
# Preserve: system_prompt, tool_definition
# Summarize: conversation (old), tool_output (resolved), retrieved_document (used)
```

**NEVER DELETE policies (hard rules):**

- **Never mask or compact active error stacks** while debugging is ongoing.
- **Never remove file paths, IDs, or decisions** from the context — these are anchors.
- **Never compact system prompts or tool definitions** — they are the cheapest cache hits.
- **Never compact the latest user turn** — it drives the next action.
- **Never silently drop context on summarizer failure** — degrade gracefully by keeping the original.

### Step 7 — Context partitioning (when one window cannot hold the problem)

**Why:** When the full task exceeds the window and decomposes into ≥3 independent subtasks, split across sub-agents with isolated contexts.

**Procedure:**

1. Identify independent subtasks (research, analysis, code generation, testing).
2. Assign each to a sub-agent with its own context window.
3. Sub-agents return **structured results only** (notes, summaries, code snippets) — not raw dumps.
4. Parent agent aggregates structured results into the final answer.
5. Coordinate via a shared plan or scratchpad (see `filesystem-context`).

> **Load `references/optimization_techniques.md`** for partitioning sketches and coordination patterns.

### Decision flowchart (quick)

```
Context pressure or cost/latency pain?
├─ Quality collapsed without clear size pressure? ──yes──► context-degradation
├─ Need durable multi-cycle handoff summary / probes? ──yes──► context-compression
├─ Raw dumps re-fetchable from disk cheaply? ──yes──► filesystem offload + pointers
└─ Optimize (this skill)
   ├─ Stable prefixes + caching runtime? ──► stabilize order / design_stable_prompt
   ├─ Tool outputs dominate? ──► ObservationStore.mask (+ retrieve tool)
   ├─ Retrieval noise? ──► scope top_k / summarize docs
   ├─ History still large at ≥70–80%? ──► compact (preserve system + latest)
   └─ Still does not fit and ≥3 subtasks? ──► partition agents
Always: baseline → one change → measure → gate on tokens AND quality
```

---

## Pitfalls

### KV-cache / prefix

- **Interleaving user-specific data into the system prompt "for convenience"** — invalidates the cache prefix every turn. Move dynamic data to trailing messages.
- **Re-sorting tools alphabetically sometimes and not others** — changes the byte sequence of the tool definition block. Pick one order and freeze it.
- **Injecting the full conversation into a hashed prefix field that changes every turn** — the hash changes, the cache misses.
- **Assuming cache works without confirming** — vLLM, TGI, TensorRT-LLM, and provider APIs all have different cache semantics. Verify with `calculate_cache_metrics` before trusting hit-rate claims.

### Observation masking

- **Masking active errors** — the model needs the full stack trace to debug. Only mask resolved, successful outputs.
- **Losing the retrieve path** — if the agent cannot call `get_ref(ref_id)`, masked observations are permanently lost. Always wire the retrieve tool.
- **LLM-generated keys instead of deterministic keys** — adds latency per turn and can hallucinate. Use `extract_key` for deterministic summaries.
- **Leaking sensitive content in placeholders** — the placeholder text is visible in logs. Do not include sensitive data beyond what the agent already saw.
- **No TTL/eviction on the reference store** — memory grows unbounded in long sessions. Set `max_size` and eviction policy.

### Token estimation

- **Trusting the `~4 chars/token` heuristic for billing** — it is a hot-path estimate only. Code, JSON, and non-English text have different densities (often 2–3× higher token/char ratio). Always reconcile with provider billed tokens.
- **Not accounting for summarizer cost** — a compaction step that calls an LLM to summarize costs tokens too. Net cost = (saved tokens) − (summarizer tokens). If negative, it is a loss.

### Compaction

- **Compacting without preserving anchors** — file paths, IDs, decisions, and open questions must survive compaction. Dropping them causes the agent to lose track of state.
- **Compacting system prompts or tool definitions** — these are the cheapest cache hits. Compacting them saves nothing and breaks caching.
- **Silent state loss on summarizer failure** — if the summarizer throws or returns empty, do not drop the original context. Degrade gracefully.

### Retrieval scoping

- **Scoring candidates against the whole session instead of the current subtask** — relevance drifts as the task progresses. Re-score per subtask.
- **Preloading "everything that might help"** — prefer just-in-time retrieval. Preloading burns budget on noise.

### Partitioning

- **Sub-agents returning raw dumps** — defeats the purpose. Sub-agents must return structured notes only.
- **Partitioning when subtasks are not independent** — coordination overhead exceeds the capacity gain. Only partition when ≥3 subtasks are truly independent.

### Security

- **Treating the reference store as untrusted without access controls** — the store is a trust boundary. Access-control refs, set TTL/eviction, and audit access patterns for sensitive workloads.

---

## Verification

Establish a **no-optimization baseline**, then enable strategies **one at a time**. Remove anything that fails its gate — pure overhead is worse than no optimization.

### Run the skill demo

From the skill root (`~\.cursor\skills\context-optimization\`):

```powershell
python scripts\compaction.py
```

Expected coverage: token estimate, observation mask + retrieve, budget utilization + `should_optimize`, stable prompt rewrite, tool-output summary. All paths should execute without exceptions.

### Verification checklist

- [ ] **Run the skill demo** — `python scripts\compaction.py` from the skill root; confirm mask/retrieve, budget trigger, and stable prompt paths work.
- [ ] **Token reduction** — average tokens/task down vs baseline; reconcile estimators with provider billed counts.
- [ ] **Net cost** — include summarizer calls; a compaction step that costs more than it saves is a loss.
- [ ] **Latency / cache** — end-to-end latency and KV-cache hit rate improve when prefixes are stabilized.
- [ ] **Quality preservation** — A/B or automated eval (e.g. Ragas, custom LLM judges); exercise **retrieve-after-mask** paths explicitly.
- [ ] **Budget-policy adherence** — simulate over-budget categories; each trigger fires the expected action; log activations.
- [ ] **Graceful failure** — empty context, oversized single message, summarizer exception → no silent state loss.
- [ ] **Security** — reference store access controls and TTL/eviction reviewed for sensitive workloads.
- [ ] **Stability in production** — error rates and re-fetch spikes watched after rollout; back off aggression if quality drops.

### Suggested gates (defaults)

| Metric | Default bar |
|--------|-------------|
| Token reduction | ≥30% on the optimized span (tune per tactic) |
| Quality drop | ≤5 absolute points (0–1 scale) |
| Masking quality impact | ≤~2% when retrieve works |
| Cache hit rate (stable workloads) | ≥70% after prefix work |

### Verification gate example

```python
from __future__ import annotations

import time
from dataclasses import dataclass
from statistics import mean
from typing import Callable, Sequence


@dataclass(frozen=True)
class OptimizationMetrics:
    label: str
    token_count: int
    latency_seconds: float
    quality_score: float

    def __post_init__(self) -> None:
        if not self.label.strip():
            raise ValueError("label must be non-empty")
        if self.token_count < 0:
            raise ValueError("token_count must be non-negative")
        if self.latency_seconds < 0:
            raise ValueError("latency_seconds must be non-negative")
        if not 0.0 <= self.quality_score <= 1.0:
            raise ValueError("quality_score must be a fraction between 0 and 1")


def measure(
    label: str,
    run_task: Callable[[], str],
    *,
    token_counter: Callable[[str], int],
    quality_scorer: Callable[[str], float],
) -> OptimizationMetrics:
    started = time.perf_counter()
    output = run_task()
    elapsed = time.perf_counter() - started
    if not output.strip():
        raise RuntimeError(f"task {label!r} produced no output")
    return OptimizationMetrics(
        label=label,
        token_count=token_counter(output),
        latency_seconds=elapsed,
        quality_score=quality_scorer(output),
    )


def assert_optimization_is_safe(
    baseline: OptimizationMetrics,
    optimized: OptimizationMetrics,
    *,
    min_token_reduction: float = 0.30,
    max_quality_drop: float = 0.05,
) -> None:
    if baseline.token_count <= 0:
        raise ValueError("baseline token_count must be positive to compute reduction")
    reduction = 1.0 - optimized.token_count / baseline.token_count
    quality_drop = baseline.quality_score - optimized.quality_score
    if reduction < min_token_reduction:
        raise AssertionError(
            f"{optimized.label}: token reduction {reduction:.1%} "
            f"is below the required {min_token_reduction:.0%}"
        )
    if quality_drop > max_quality_drop:
        raise AssertionError(
            f"{optimized.label}: quality dropped {quality_drop:.1%}, "
            f"exceeding the allowed {max_quality_drop:.0%}"
        )


def summarize_runs(runs: Sequence[OptimizationMetrics]) -> str:
    if not runs:
        raise ValueError("no runs to summarize")
    return (
        f"runs={len(runs)} "
        f"avg_tokens={mean(run.token_count for run in runs):.0f} "
        f"avg_latency={mean(run.latency_seconds for run in runs):.3f}s "
        f"avg_quality={mean(run.quality_score for run in runs):.3f}"
    )
```

---

## Examples

### Example A — Tool outputs dominate

1. Agent loop logs `tool_outputs` at 45% of a 128k window.
2. Policy fires `mask_oldest_resolved_observations`.
3. Utilization drops; history still at 75%.
4. `compact_message_history` runs with system + last turn preserved.
5. Metrics: tokens −40%, quality −2%, latency improved if cache hit rate also rose.

### Example B — Cache miss rate high

1. Metrics: miss rate 0.55, prefix includes `Current time: {iso}`.
2. Action: `design_stable_prompt` / move time to trailing message; freeze tool list order.
3. Re-measure hit rate; expect material TTFT drop on warm prefixes.

### Example C — Retrieval thrash

1. Last 10 retrievals: 3 relevant, 7 distractors.
2. Tighten `top_k`, raise score threshold, summarize retained docs with `summarize_content(..., "retrieved_document")`.
3. If research still huge, partition into a research sub-agent that returns notes only.

---

## Related Skills

| Skill | Relationship |
|-------|----------------|
| `context-fundamentals` | Why windows and attention behave as they do |
| `context-degradation` | Diagnose lost-in-middle, poisoning, clash, distraction |
| `context-compression` | Lossy structured summaries, multi-cycle drift, probe eval |
| `filesystem-context` | Verbatim offload of large artifacts |
| `multi-agent-patterns` | Isolation and coordination for partitioned work |
| `memory-systems` | Persistent retrieval feeding context just in time |
| `evaluation` / advanced evaluation | Task quality gates beyond token counts |
| `tool-use-orchestration` | Tool-call patterns that shape observation volume |
| `prompt-engineering-advanced` | Signal-dense prompts that reduce noise at the source |

---

## References

### Internal (this skill)

| Path | When to load |
|------|--------------|
| `scripts/compaction.py` | Implementing or adapting `ObservationStore`, `ContextBudget`, token estimates, category summaries, `design_stable_prompt`, cache metrics. Run `python scripts\compaction.py` to verify all paths. |
| `references/optimization_techniques.md` | Thresholds, selective masking patterns, partitioning sketches, monitoring alerts, integration patterns, performance targets. Load when core procedure is insufficient for your use case. |

### External anchors (conceptual)

- Provider docs for **prompt caching / prefix caching** on your stack (OpenAI, Anthropic, Gemini, vLLM, TGI, TensorRT-LLM)
- Liu et al., 2023 — *Lost in the Middle* (placement still matters after you shrink context)
- Inference engine docs for **KV-cache hit metrics** used in your monitoring
