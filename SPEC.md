# SPEC.md: "The Human How"; Exact technical requirements, environment setup, implementation details, etc.

## Execution Description

Same SGLang/tiny_sglang server split as 130 on port 30000. Then runs scripts/bench_serving.py concurrently. num_prompts, max_concurrency, input_len, and output_len come from yaml. Do not use prompt_response_client.py. Sweep dimensions: dtype, input_len, output_len, max_total_tokens, max_concurrency, request_rate, num_prompts, seed.

## Parameters

| Parameter | CLI Flag | Tested Values | Default | Description |
| --- | --- | --- | --- | --- |
| dtype | `--dtype` | smoke=bfloat16, baseline=bfloat16, extended=bfloat16 | bfloat16 | From Parameter list; see Execution Description With Parameters. |
| input_len | `--input-len` | smoke=64, baseline=128, extended=128 | 128 | From Parameter list; see Execution Description With Parameters. |
| output_len | `--output-len` | smoke=16, baseline=840, extended=1700 | 840 | From Parameter list; see Execution Description With Parameters. |
| max_total_tokens | `--max-total-tokens` | smoke=2048, baseline=16384, extended=32768 | 16384 | From Parameter list; see Execution Description With Parameters. |
| max_concurrency | `--max-concurrency` | smoke=2, baseline=8, extended=8 | 8 | From Parameter list; see Execution Description With Parameters. |
| request_rate | `--request-rate` | smoke=inf, baseline=inf, extended=inf | inf | From Parameter list; see Execution Description With Parameters. |
| num_prompts | `--num-prompts` | smoke=2, baseline=24, extended=48 | 24 | From Parameter list; see Execution Description With Parameters. |
| seed | `--seed` | smoke=42, baseline=42, extended=42 | 42 | From Parameter list; see Execution Description With Parameters. |

## Invocation

```bash
Smoke starts scripts/tiny_sglang_server.py; baseline and extended start python -m sglang.launch_server, then scripts/bench_serving.py
```

## Raw Output Format

raw_results.csv with one req_N row per request and a final summary row. Each request row also carries the run-level p50 metrics

check_name,status,e2e_ms,ttft_ms,tpot_ms,end_to_end_request_latency_p50_msec,ttft_p50_msec,time_per_output_token_tpot_p50_msec,inter_token_latency_itl_p50_msec,request_throughput_requests_sec
req_0,ok,400,40,12,380,35,11,10,4.5
summary,ok,380,35,11,380,35,11,10,4.5

## Metrics

- **#1: E2E latency, ms** — stored as `end_to_end_request_latency_p50_msec`.
- **#2: TTFT, ms** — stored as `ttft_p50_msec`.
- **#3: TPOT, ms** — stored as `time_per_output_token_tpot_p50_msec`.
- **#4: ITL, ms** — stored as `inter_token_latency_itl_p50_msec`.
- **#5: Request throughput** — stored as `request_throughput_requests_sec`.

## Framework

Same SGLang/tiny_sglang server split as 130 on port 30000. Then runs scripts/bench_serving.py concurrently. num_prompts, max_concurrency, input_len, and output_len come from yaml.

## Installation and Execution Summary

Launch scripts/tiny_sglang_server.py on smoke, or python -m sglang.launch_server with Mistral-7B-v0.3 on baseline and extended, on port 30000. Then run scripts/bench_serving.py with yaml num_prompts and max_concurrency, to measure concurrent serving latency and throughput. This is not prompt_response_client.py

## Platform Portability

- **AMD (primary):** ```bash
Smoke starts scripts/tiny_sglang_server.py; baseline and extended start python -m sglang.launch_server, then scripts/bench_serving.py
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

raw_results.csv with one req_N row per request and a final summary row. Each request row also carries the run-level p50 metrics

check_name,status,e2e_ms,ttft_ms,tpot_ms,end_to_end_request_latency_p50_msec,ttft_p50_msec,time_per_output_token_tpot_p50_msec,inter_token_latency_itl_p50_msec,request_throughput_requests_sec
req_0,ok,400,40,12,380,35,11,10,4.5
summary,ok,380,35,11,380,35,11,10,4.5

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
5. All required aggregate metrics are physically sensible (positive values). Same SGLang/tiny_sglang server split as 130 on port 30000. Then runs scripts/bench_serving.py concurrently. num_prompts, max_concurrency, input_len, and output_len come from yaml.
6. At least 2 sample rows exist for the latest `run_id` (sweep coverage).
7. No sample has `status = 'error'`.
8. Same SGLang/tiny_sglang server split as 130 on port 30000. Then runs scripts/bench_serving.py concurrently. num_prompts, max_concurrency, input_len, and output_len come from yaml.

### Baseline / Threshold configuration (`config/benchmark_config.yaml`)

Expected ranges and gates live in `config/benchmark_config.yaml` under `baselines:` or `thresholds:`. To update them, edit that file — never edit validation code directly.

Threshold key suffixes encode comparison direction when `thresholds:` is present: `_min` → observed value must be ≥ threshold. `_max` → observed value must be ≤ threshold. Informational `baselines:` ranges are not pass/fail gates.
