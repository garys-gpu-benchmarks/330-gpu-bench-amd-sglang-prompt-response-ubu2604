# Results: 330 SGLang Prompt-Response Benchmark

**Main goal:** Measure sequential SGLang prompt-response latency  
**Tools:** Every profile starts sglang.launch_server with Mistral-7B-v0.3, then prompt_response_client.py

Reference results measured by the maintainer on the system shown in each section (generator TEMPLATE_00_138 · recorded 2026-11-13). Your own runs write `results/RESULTS_ACTUAL.md` in the same format.

> Smoke runs use deliberately small sizes. They check that the workload installs and runs end to end; their numbers are not performance results. Baseline and extended are.

<!-- profile:smoke -->
## smoke

- **System:** 1× AMD Instinct MI300X VF (196288 MB) · INTEL(R) XEON(R) PLATINUM 8568Y+ · Ubuntu 26.04.1 LTS · kernel 7.0.0-38-generic · driver 7.1.9.31600000
- **Run:** 2026-11-13T00:04:21Z · status **PASS** (exit 0)
- **Time (mm:ss):** setup 08:26 · benchmark 02:42 · whole run 12:17
- **Source:** run 330_20261113_000421
- **Command:** `bash run_benchmark.sh --smoke --dtype bfloat16 --input-len 64 --output-len 16 --model-name mistralai/Mistral-7B-v0.3 --num-prompts 2 --max-running-requests 1 --max-total-tokens 1024 --seed 42`

| # | Metric | Result |
|---|---|---|
| 1 | E2E latency p50, ms (end_to_end_latency_ms) | 315.4523295006584 |
| 2 | Time To First Token p50, ms (ttft_ms) | 39.91399350070424 |
| 3 | Output token generation rate (decode) p50, tokens/s (output_tokens_per_s) | 55.27537682108462 |
| 4 | RadixAttn hit rate, pct (radixattention_cache_hit_rate_pct) | 26.865671641791046 |
| 5 | Completed request rate, pct (completed_request_rate_pct) | 100.0 |
<!-- /profile:smoke -->
