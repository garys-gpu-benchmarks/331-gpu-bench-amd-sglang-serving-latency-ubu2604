# PRD.md:  "The Why"; Product requirements, benchmark metadata table, high-level requirements, etc.

Product Requirements Document

"The Why"; Product requirements, benchmark metadata table, high-level requirements, etc. Defines the benchmark goal, validation objective, test name, benchmark number, category, and high-level success criteria.

## Benchmark Matrix Document Metadata (via benchmark_specification.json)

This PRD.md section is populated from benchmark_specification.json, which is the structured source of benchmark-specific product requirements.

## Workload Number
331

## Workload Name
SGLang Serving Latency Benchmark

## Execution Summary (Run and Measure)
Launch scripts/tiny_sglang_server.py on smoke, or python -m sglang.launch_server with Mistral-7B-v0.3 on baseline and extended, on port 30000. Then run scripts/bench_serving.py with yaml num_prompts and max_concurrency, to measure concurrent serving latency and throughput. This is not prompt_response_client.py

## Main Goal
Measure SGLang concurrent serving latency percentiles under load

## Validation Objective
Validates concurrent serving latency percentiles and request throughput. This is not sequential prompt-response. Smoke uses scripts/tiny_sglang_server.py; baseline and extended use sglang.launch_server

## Workload Category
LLM Inference & Serving

## Validation Requirement

The benchmark must include an automated SQLite-integrated validation layer that verifies persisted results from `results/benchmark.db`. Validation must confirm:

1. The benchmark run completed successfully with no tool errors.
2. Required samples and aggregate metrics were persisted for every swept shape.
3. Metrics are finite and physically sensible (positive, within plausible bounds).
4. Measured values satisfy configured thresholds when the workload defines pass/fail gates.
5. The benchmark fails validation when required data is missing, invalid, or outside bounds.

## Non-Functional Requirements

| Requirement | Target |
|---|---|
| Automation | Runs to completion without manual intervention after `bash run_benchmark.sh` |
| Idempotency | Re-running `run_benchmark.sh` appends a new run; never corrupts existing rows |
| Persistence | All metrics survive script exit; `results/benchmark.db` is the durable record |
