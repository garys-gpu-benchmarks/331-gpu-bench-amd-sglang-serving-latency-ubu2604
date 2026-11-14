# Results: 331 SGLang Serving Latency Benchmark

**Main goal:** Measure SGLang concurrent serving latency percentiles under load  
**Tools:** Every profile starts sglang.launch_server with Mistral-7B-v0.3, then bench_serving.py

Reference results measured by the maintainer on the system shown in each section (generator TEMPLATE_00_140 · recorded 2026-11-13). Your own runs write `results/RESULTS_ACTUAL.md` in the same format.

> Smoke runs use deliberately small sizes. They check that the workload installs and runs end to end; their numbers are not performance results. Baseline and extended are.

<!-- profile:smoke -->
## smoke

- **System:** 1× AMD Instinct MI300X VF (196288 MB) · INTEL(R) XEON(R) PLATINUM 8568Y+ · Ubuntu 26.04.1 LTS · kernel 7.0.0-38-generic · driver 7.1.9.31600000
- **Run:** 2026-11-13T21:30:08Z · status **PASS** (exit 0)
- **Time (mm:ss):** setup 04:27 · benchmark 02:23 · whole run 08:00
- **Source:** run 331_20261113_213008
- **Command:** `bash run_benchmark.sh --smoke --dtype bfloat16 --input-len 64 --output-len 16 --max-total-tokens 1024 --model-name mistralai/Mistral-7B-v0.3 --max-concurrency 2 --max-running-requests 2 --num-prompts 2`

| # | Metric | Result |
|---|---|---|
| 1 | E2E latency p50, client-observed from send, ms (end_to_end_request_latency_p50_msec) | 306.35716400047386 |
| 2 | TTFT p50, client-observed from send, ms (ttft_p50_msec) | 40.550178000557935 |
| 3 | TPOT, ms (time_per_output_token_tpot_p50_msec) | 16.639822666669108 |
| 4 | ITL, ms (inter_token_latency_itl_p50_msec) | 16.458059999422403 |
| 5 | Request throughput (request_throughput_requests_sec) | 6.513369118371552 |
<!-- /profile:smoke -->
