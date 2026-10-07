# SPEC.md: "The Human How"; Exact technical requirements, environment setup, implementation details, etc.

## Execution Description

Starts tiny_sglang_server.py on smoke, or python -m sglang.launch_server with Mistral-7B-v0.3 --dtype bfloat16 on baseline/extended (port 30000, aiter enabled). Then runs scripts/prompt_response_client.py sequentially. Do not use bench_serving.py. output_format: csv Sweep dimensions: dtype, input_len, output_len, num_prompts, seed, streaming.

## Parameters

| Parameter | CLI Flag | Tested Values | Default | Description |
| --- | --- | --- | --- | --- |
| dtype | `--dtype` | smoke=bfloat16, baseline=bfloat16, extended=bfloat16 | bfloat16 | From Parameter list; see Execution Description With Parameters. |
| input_len | `--input-len` | smoke=64, baseline=512, extended=1024 | 512 | From Parameter list; see Execution Description With Parameters. |
| output_len | `--output-len` | smoke=16, baseline=750, extended=5270 | 750 | From Parameter list; see Execution Description With Parameters. |
| num_prompts | `--num-prompts` | smoke=2, baseline=8, extended=8 | 8 | From Parameter list; see Execution Description With Parameters. |
| seed | `--seed` | smoke=42, baseline=42, extended=42 | 42 | From Parameter list; see Execution Description With Parameters. |
| streaming | `--streaming` | smoke=true, baseline=true, extended=true | true | From Parameter list; see Execution Description With Parameters. |

## Invocation

```bash
Smoke starts scripts/tiny_sglang_server.py; baseline and extended start python -m sglang.launch_server, then scripts/prompt_response_client.py
```

## Raw Output Format

CSV with one row per prompt, plus a printed summary line

request_id,sample_index,status,input_tokens,output_tokens,end_to_end_latency_ms,ttft_ms,output_tokens_per_s,completed_request_rate_pct,radixattention_cache_hit_rate_pct,prompt_tokens,cached_tokens,error_message
0,0,ok,128,32,400,40,80,100,0,128,0,

## Metrics

- **#1: E2E latency, ms** — stored as `end_to_end_latency_ms`.
- **#2: Time To First Token** — stored as `ttft_ms`.
- **#3: Output token generation rate** — stored as `decode`.
- **#4: RadixAttn hit rate, pct** — stored as `radixattention_cache_hit_rate_pct`.
- **#5: Completed request rate, pct** — stored as `completed_request_rate_pct`.

## Framework

Starts tiny_sglang_server.py on smoke, or python -m sglang.launch_server with Mistral-7B-v0.3 --dtype bfloat16 on baseline/extended (port 30000, aiter enabled). Then runs scripts/prompt_response_client.py sequentially. Do not use bench_serving.py.

## Installation and Execution Summary

Launch scripts/tiny_sglang_server.py on smoke, or python -m sglang.launch_server with Mistral-7B-v0.3 on baseline and extended, on 127.0.0.1:30000. Then run scripts/prompt_response_client.py one request at a time, to measure sequential prompt-response latency. This is not bench_serving.py

## Platform Portability

- **AMD (primary):** ```bash
Smoke starts scripts/tiny_sglang_server.py; baseline and extended start python -m sglang.launch_server, then scripts/prompt_response_client.py
```
- **NVIDIA:** Primary target is AMD ROCm. NVIDIA notes in this section are reference only and are not the execution path.

## Model Context Protocols

- **Active:** None

## Execution-Loop Validation Contract

EXECUTION CHAIN: `run_benchmark.sh` ➔ raw output ➔ `scripts/parse_results.py` ➔ `results/benchmark.db` ➔ `scripts/validate_results.py`

This benchmark uses a lightweight, SQLite-integrated execution loop for result validation. All validation is performed by `scripts/validate_results.py`.

### Validation script usage

```bash
export BENCHMARK_PYTHON=/usr/bin/python3.13  # optional; select the installed interpreter

# After a live run:
".venv/bin/python" scripts/validate_results.py --db results/benchmark.db

# CI / no-GPU path (seeds fixture and validates it):
".venv/bin/python" scripts/validate_results.py --seed-fixture --quiet

# Override DB path via environment variable:
BENCHMARK_DB=tests/fixtures/benchmark.db \
  ".venv/bin/python" scripts/validate_results.py
```

### Run artifact contract

CSV with one row per prompt, plus a printed summary line

request_id,sample_index,status,input_tokens,output_tokens,end_to_end_latency_ms,ttft_ms,output_tokens_per_s,completed_request_rate_pct,radixattention_cache_hit_rate_pct,prompt_tokens,cached_tokens,error_message
0,0,ok,128,32,400,40,80,100,0,128,0,

```bash
bash run_benchmark.sh --help
bash run_benchmark.sh --profile smoke --validate
bash run_benchmark.sh --profile baseline --validate
bash run_benchmark.sh --profile extended --validate
```
`run_benchmark.sh --help` prints usage and exits. The harness calls `scripts/ensure_setup.sh` when `.setup_state` is absent.

### Required integrity checks (built into `validate_results.py`)

1. Latest run exists and `runs.status = 'ok'`.
2. `run.error_message` is NULL.
3. `started_at` and `finished_at` are valid ISO-8601 UTC strings.
4. All required aggregate metrics in `runs` are non-NULL and finite.
5. All required aggregate metrics are physically sensible (positive values). Starts tiny_sglang_server.py on smoke, or python -m sglang.launch_server with Mistral-7B-v0.3 --dtype bfloat16 on baseline/extended (port 30000, aiter enabled). Then runs scripts/prompt_response_client.py sequentially. Do not use bench_serving.py.
6. At least 2 sample rows exist for the latest `run_id` (sweep coverage).
7. No sample has `status = 'error'`.
8. Starts tiny_sglang_server.py on smoke, or python -m sglang.launch_server with Mistral-7B-v0.3 --dtype bfloat16 on baseline/extended (port 30000, aiter enabled). Then runs scripts/prompt_response_client.py sequentially. Do not use bench_serving.py.

### Baseline / Threshold configuration (`config/benchmark_config.yaml`)

Expected ranges and gates live in `config/benchmark_config.yaml` under `baselines:` or `thresholds:`. To update them, edit that file — never edit validation code directly.

Threshold key suffixes encode comparison direction when `thresholds:` is present: `_min` → observed value must be ≥ threshold. `_max` → observed value must be ≤ threshold. Informational `baselines:` ranges are not pass/fail gates.
