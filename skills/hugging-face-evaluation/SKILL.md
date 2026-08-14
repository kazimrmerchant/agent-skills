---
name: hugging-face-evaluation
description: Add and manage evaluation results in Hugging Face model cards. Use when extracting eval tables from README content, importing scores from Artificial Analysis API, or running custom model evaluations with vLLM/lighteval/inspect-ai.
version: 1.3.1
risk: unknown
source: community
---

## When to Use

- You need to add structured evaluation results to a Hugging Face model card.
- You want to import benchmark data from Artificial Analysis into a model card.
- You are running custom model evaluations with vLLM, lighteval, or inspect-ai.
- You are preparing leaderboard-compatible `model-index` metadata for a model release.
- You need to inspect or extract evaluation tables from a README before pushing changes.

Trigger keywords: `hugging face evaluation`, `model card`, `model-index`, `lighteval`, `inspect-ai`, `vLLM evaluation`, `Artificial Analysis`, `benchmark scores`, `leaderboard`, `eval table`.

## Prerequisites

- **uv** installed (preferred). PEP 723 script headers auto-install dependencies when using `uv run`.
- Alternatively, install manually: `pip install huggingface-hub markdown-it-py python-dotenv pyyaml requests`
- `HF_TOKEN` environment variable set with **write-access** token for the target repository.
- For Artificial Analysis imports: `AA_API_KEY` environment variable set.
- `.env` is loaded automatically if `python-dotenv` is installed.
- For vLLM evaluation: GPU available (`nvidia-smi` to verify), sufficient GPU memory for the model size.
- All paths are relative to the directory containing this SKILL.md. `cd` to that directory or use full paths before running scripts.

## Procedure

### Core Workflow (README Extraction)

The recommended flow matches the CLI `--help` output:

1. **Check for existing PRs** (REQUIRED before any `--create-pr`):
   ```bash
   uv run scripts/evaluation_manager.py get-prs --repo-id "username/model-name"
   ```
   If open PRs exist, do NOT create a new PR. Warn the user, show the PR URLs, and only proceed if the user explicitly confirms.

2. **Inspect tables** to find table numbers and column structure:
   ```bash
   uv run scripts/evaluation_manager.py inspect-tables --repo-id "username/model-name"
   ```

3. **Extract a specific table** (prints YAML by default — review before applying):
   ```bash
   uv run scripts/evaluation_manager.py extract-readme \
     --repo-id "username/model-name" \
     --table 1 \
     [--model-column-index <column index from inspect-tables>] \
     [--model-name-override "<exact column header text>"] \
     [--task-type "text-generation"] \
     [--dataset-name "Custom Benchmarks"]
   ```

4. **Apply changes** (push directly or create PR):
   ```bash
   # Push directly
   uv run scripts/evaluation_manager.py extract-readme \
     --repo-id "username/model-name" \
     --table 1 \
     --apply

   # Or open a PR
   uv run scripts/evaluation_manager.py extract-readme \
     --repo-id "username/model-name" \
     --table 1 \
     --create-pr
   ```

### Method 2: Import from Artificial Analysis

Fetch benchmark scores from the Artificial Analysis API and add them to a model card.

```bash
# Inline API key
AA_API_KEY="YOUR_KEY" uv run scripts/evaluation_manager.py import-aa \
  --creator-slug "anthropic" \
  --model-name "claude-sonnet-4" \
  --repo-id "username/model-name"

# With .env file
echo "AA_API_KEY=YOUR_KEY" >> .env
echo "HF_TOKEN=YOUR_KEY" >> .env
uv run scripts/evaluation_manager.py import-aa \
  --creator-slug "anthropic" \
  --model-name "claude-sonnet-4" \
  --repo-id "username/model-name"

# Create a PR (check get-prs first!)
uv run scripts/evaluation_manager.py get-prs --repo-id "username/model-name"
uv run scripts/evaluation_manager.py import-aa \
  --creator-slug "anthropic" \
  --model-name "claude-sonnet-4" \
  --repo-id "username/model-name" \
  --create-pr
```

### Method 3: Run Evaluation Job (Inference Providers)

Submit an evaluation job on Hugging Face infrastructure using `hf jobs uv run`.

```bash
# CPU
HF_TOKEN=$HF_TOKEN \
hf jobs uv run hf-evaluation/scripts/inspect_eval_uv.py \
  --flavor cpu-basic \
  --secret HF_TOKEN=$HF_TOKEN \
  -- --model "meta-llama/Llama-2-7b-hf" \
     --task "mmlu"

# GPU (A10G)
HF_TOKEN=$HF_TOKEN \
hf jobs uv run hf-evaluation/scripts/inspect_eval_uv.py \
  --flavor a10g-small \
  --secret HF_TOKEN=$HF_TOKEN \
  -- --model "meta-llama/Llama-2-7b-hf" \
     --task "gsm8k"
```

Python helper:
```bash
uv run scripts/run_eval_job.py \
  --model "meta-llama/Llama-2-7b-hf" \
  --task "mmlu" \
  --hardware "t4-small"
```

### Method 4: Run Custom Model Evaluation with vLLM

> **Important:** Only possible on devices with `uv` installed and sufficient GPU memory. No need to use `hf_jobs()` MCP tool — run scripts directly in terminal.

Before running:
- Verify the script path exists under `scripts/`.
- Verify `uv` is installed.
- Verify GPU is available: `nvidia-smi`

#### Option A: lighteval with vLLM Backend

```bash
# MMLU 5-shot with vLLM
uv run scripts/lighteval_vllm_uv.py \
  --model meta-llama/Llama-3.2-1B \
  --tasks "leaderboard|mmlu|5"

# Multiple tasks
uv run scripts/lighteval_vllm_uv.py \
  --model meta-llama/Llama-3.2-1B \
  --tasks "leaderboard|mmlu|5,leaderboard|gsm8k|5"

# Accelerate backend instead of vLLM
uv run scripts/lighteval_vllm_uv.py \
  --model meta-llama/Llama-3.2-1B \
  --tasks "leaderboard|mmlu|5" \
  --backend accelerate

# Chat/instruction-tuned models
uv run scripts/lighteval_vllm_uv.py \
  --model meta-llama/Llama-3.2-1B-Instruct \
  --tasks "leaderboard|mmlu|5" \
  --use-chat-template
```

Via HF Jobs:
```bash
hf jobs uv run scripts/lighteval_vllm_uv.py \
  --flavor a10g-small \
  --secrets HF_TOKEN=$HF_TOKEN \
  -- --model meta-llama/Llama-3.2-1B \
     --tasks "leaderboard|mmlu|5"
```

**lighteval task format:** `suite|task|num_fewshot`
- `leaderboard|mmlu|5` — MMLU 5-shot
- `leaderboard|gsm8k|5` — GSM8K 5-shot
- `lighteval|hellaswag|0` — HellaSwag zero-shot
- `leaderboard|arc_challenge|25` — ARC-Challenge 25-shot

Full task list: https://github.com/huggingface/lighteval/blob/main/examples/tasks/all_tasks.txt (format in file is `suite|task|num_fewshot|0`; ignore trailing `0`).

#### Option B: inspect-ai with vLLM Backend

```bash
# MMLU with vLLM
uv run scripts/inspect_vllm_uv.py \
  --model meta-llama/Llama-3.2-1B \
  --task mmlu

# HuggingFace Transformers backend
uv run scripts/inspect_vllm_uv.py \
  --model meta-llama/Llama-3.2-1B \
  --task mmlu \
  --backend hf

# Multi-GPU with tensor parallelism
uv run scripts/inspect_vllm_uv.py \
  --model meta-llama/Llama-3.2-70B \
  --task mmlu \
  --tensor-parallel-size 4
```

Via HF Jobs:
```bash
hf jobs uv run scripts/inspect_vllm_uv.py \
  --flavor a10g-small \
  --secrets HF_TOKEN=$HF_TOKEN \
  -- --model meta-llama/Llama-3.2-1B \
     --task mmlu
```

Available inspect-ai tasks: `mmlu`, `gsm8k`, `hellaswag`, `arc_challenge`, `truthfulqa`, `winogrande`, `humaneval`.

#### Option C: Python Helper Script

```bash
# Auto-detect hardware based on model size
uv run scripts/run_vllm_eval_job.py \
  --model meta-llama/Llama-3.2-1B \
  --task "leaderboard|mmlu|5" \
  --framework lighteval

# Explicit hardware + tensor parallelism
uv run scripts/run_vllm_eval_job.py \
  --model meta-llama/Llama-3.2-70B \
  --task mmlu \
  --framework inspect \
  --hardware a100-large \
  --tensor-parallel-size 4

# HF Transformers backend
uv run scripts/run_vllm_eval_job.py \
  --model microsoft/phi-2 \
  --task mmlu \
  --framework inspect \
  --backend hf
```

**Hardware recommendations:**

| Model Size | Recommended Hardware |
|------------|----------------------|
| < 3B params | `t4-small` |
| 3B – 13B | `a10g-small` |
| 13B – 34B | `a10g-large` |
| 34B+ | `a100-large` |

### Commands Reference

```bash
# Help and version
uv run scripts/evaluation_manager.py --help
uv run scripts/evaluation_manager.py --version

# Inspect tables (start here)
uv run scripts/evaluation_manager.py inspect-tables --repo-id "username/model-name"

# Extract from README
uv run scripts/evaluation_manager.py extract-readme \
  --repo-id "username/model-name" \
  --table N \
  [--model-column-index N] \
  [--model-name-override "Exact Column Header or Model Name"] \
  [--task-type "text-generation"] \
  [--dataset-name "Custom Benchmarks"] \
  [--apply | --create-pr]

# Import from Artificial Analysis
AA_API_KEY=YOUR_KEY uv run scripts/evaluation_manager.py import-aa \
  --creator-slug "creator-name" \
  --model-name "model-slug" \
  --repo-id "username/model-name" \
  [--create-pr]

# View / validate
uv run scripts/evaluation_manager.py show --repo-id "username/model-name"
uv run scripts/evaluation_manager.py validate --repo-id "username/model-name"

# Check open PRs (ALWAYS run before --create-pr)
uv run scripts/evaluation_manager.py get-prs --repo-id "username/model-name"
```

### Model-Index Format

Generated YAML follows this structure:

```yaml
model-index:
  - name: Model Name
    results:
      - task:
          type: text-generation
        dataset:
          name: Benchmark Dataset
          type: benchmark_type
        metrics:
          - name: MMLU
            type: mmlu
            value: 85.2
          - name: HumanEval
            type: humaneval
            value: 72.5
        source:
          name: Source Name
          url: https://source-url.com
```

**WARNING:** Do not use markdown formatting in the model name. Use the exact name from the table. Only use URLs in the `source.url` field.

### Model Name Matching

When extracting evaluation tables with multiple models, the script uses **exact normalized token matching**:
- Removes markdown formatting (`**`, links `[]()`)
- Normalizes names (lowercase, replace `-` and `_` with spaces)
- Compares token sets: `"OLMo-3-32B"` → `{"olmo", "3", "32b"}` matches `"**Olmo 3 32B**"` or `"Olmo-3-32B"`
- Only extracts if tokens match exactly (handles different word orders and separators)
- Fails if no exact match found rather than guessing

For column-based tables (benchmarks as rows, models as columns): finds the column header matching the model name, extracts scores from that column only.

For transposed tables (models as rows, benchmarks as columns): finds the row in the first column matching the model name, extracts all benchmark scores from that row only.

## Pitfalls

- **NEVER create a PR without checking `get-prs` first.** Creating duplicate PRs spams model repositories and creates extra work for maintainers. If open PRs exist, warn the user and show the URLs. Only proceed if the user explicitly confirms.
- **Always start with `inspect-tables`.** Running `extract-readme` without knowing the table number and column structure leads to wrong extractions.
- **Prefer `--model-column-index`** over `--model-name-override`. If you must use the override, the column header text must be exact (copy from `inspect-tables` output, including markdown formatting like `**`).
- **No markdown in YAML names.** The model name field in YAML must be plain text.
- **One model per repo.** Only add the main model's results to model-index, not training checkpoints or unrelated models.
- **`--table N` is required** when multiple evaluation tables exist in a README.
- **For transposed tables**, ensure only one row is extracted.
- **HF_TOKEN must have write access** for the target repository, or pushes/PRs will fail.
- **vLLM OOM / CUDA OOM**: Use a larger hardware flavor, reduce `--gpu-memory-utilization`, or use `--tensor-parallel-size` for multi-GPU.
- **Model architecture not supported by vLLM**: Use `--backend hf` (inspect-ai) or `--backend accelerate` (lighteval) for HuggingFace Transformers fallback.
- **Trust remote code required**: Add `--trust-remote-code` for models with custom code (e.g., Phi-2, Qwen).
- **Chat template not found**: Only use `--use-chat-template` for instruction-tuned models that include a chat template.
- **Payment required for hardware**: Add a payment method to your Hugging Face account to use non-CPU hardware.
- **Default behavior prints YAML** — always review the output before using `--apply` or `--create-pr`.
- **Use `--create-pr` when updating models you don't own**; use `--apply` for your own models.

## Verification

1. **Verify YAML output before applying:**
   ```bash
   uv run scripts/evaluation_manager.py extract-readme \
     --repo-id "username/model-name" \
     --table 1
   ```
   Compare the printed YAML against the README table manually.

2. **Validate model-index after applying:**
   ```bash
   uv run scripts/evaluation_manager.py validate --repo-id "username/model-name"
   ```

3. **Show current model-index:**
   ```bash
   uv run scripts/evaluation_manager.py show --repo-id "username/model-name"
   ```

4. **Check for existing PRs (before creating one):**
   ```bash
   uv run scripts/evaluation_manager.py get-prs --repo-id "username/model-name"
   ```
   Expected: lists PR number, title, author, date, and URL for each open PR. If none exist, output indicates no open PRs.

5. **Verify GPU availability (vLLM path):**
   ```bash
   nvidia-smi
   ```

6. **Verify uv is installed:**
   ```bash
   uv --version
   ```

7. **Verify script help is accessible:**
   ```bash
   uv run scripts/evaluation_manager.py --help
   uv run scripts/evaluation_manager.py inspect-tables --help
   uv run scripts/evaluation_manager.py extract-readme --help
   ```

## Related Skills

- **hugging-face-jobs** — for general HF Jobs submission and management.
- **hugging-face-spaces** — for deploying evaluation dashboards as Spaces.
- **model-card-authoring** — for creating and formatting model card README content.
