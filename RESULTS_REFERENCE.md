# Results: 331 SGLang Serving Latency Benchmark

**Main goal:** Measure SGLang concurrent serving latency percentiles under load  
**Tools:** Every profile starts sglang.launch_server with Mistral-7B-v0.3, then bench_serving.py

Reference results measured by the maintainer on the system shown in each section (generator TEMPLATE_00_138 · recorded 2026-11-13). Your own runs write `results/RESULTS_ACTUAL.md` in the same format.

> Smoke runs use deliberately small sizes. They check that the workload installs and runs end to end; their numbers are not performance results. Baseline and extended are.

<!-- profile:smoke -->
## smoke

- **System:** 1× AMD Instinct MI300X VF (196288 MB) · INTEL(R) XEON(R) PLATINUM 8568Y+ · Ubuntu 26.04.1 LTS · kernel 7.0.0-38-generic · driver 7.1.9.31600000
- **Run:** 2026-11-13T00:13:03Z · status **PASS** (exit 0)
- **Time (mm:ss):** setup 04:21 · benchmark 02:18 · whole run 07:47
- **Source:** run 331_20261113_001303
- **Command:** `bash run_benchmark.sh --smoke --dtype bfloat16 --input-len 64 --output-len 16 --max-total-tokens 1024 --model-name mistralai/Mistral-7B-v0.3 --max-concurrency 2 --max-running-requests 2 --num-prompts 2`

| # | Metric | Result |
|---|---|---|
| 1 | E2E latency p50, client-observed from send, ms (end_to_end_request_latency_p50_msec) | 310.68387899995287 |
| 2 | TTFT p50, client-observed from send, ms (ttft_p50_msec) | 37.26802399978624 |
| 3 | TPOT, ms (time_per_output_token_tpot_p50_msec) | 17.114674666663632 |
| 4 | ITL, ms (inter_token_latency_itl_p50_msec) | 16.372023999792873 |
| 5 | Request throughput (request_throughput_requests_sec) | 6.42650667689926 |
<!-- /profile:smoke -->
