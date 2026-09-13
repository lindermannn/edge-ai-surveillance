# Benchmarks — where the numbers stand today

This file exists so the *Targets* table in the README reads as targets, not results. Here is what is actually measured versus what is instrumented-but-not-yet-run.

## What is measured today

The x86/Windows prototype (see *Current vs. target* in the README) runs against live RTSP streams, and the private repository includes an automated benchmark harness (`benchmark.py`) that evaluates a recorded session against explicit pass/fail criteria — not a one-off number, a repeatable check:

| Criterion | Threshold | What it protects against |
|---|---|---|
| Inference FPS | ≥ 90% of target FPS | silent throughput degradation |
| Event latency (p95) | < 2000 ms | slow alerts, not just slow frames |
| Dropped-frame ratio | < 1% | camera desync under load |
| Temperature | < 75 °C | thermal throttling on sustained load |
| Memory (RSS slope) | < 10 MB/hour, < 1.20× baseline after warmup | slow leaks that only show up over hours |
| Process restarts | tracked per camera | watchdog masking a real failure |

The 90-minute warmup window and the 24-hour evaluation horizon come from `docs/RK3576_VALIDATION_PLAN.md` (private repo) — the same document that sets these thresholds. The logic runs; there is a test suite behind it.

## What is not measured yet

**No 24-hour run has been executed and recorded against these criteria.** The harness exists; the campaign hasn't happened. Concretely, that means:

- The `<200 ms glass-to-glass` and `4 streams at 15+ FPS` figures in the README's *Targets* table are the design targets fed into this harness, not its output yet.
- Everything above was validated for correctness (the benchmark's own test suite passes, on synthetic and short recorded sessions) — not yet run as a real 24h campaign on live cameras.
- The RK3576/NPU hardware migration hasn't happened, so none of this has been measured on the target device at all. Today's numbers, once the campaign runs, will describe the x86/Windows prototype.

## Why this file exists

Publishing "< 200ms" as if it were measured, when it's a threshold nothing has been checked against yet, is the exact failure this project is trying to avoid elsewhere — the same reason the RAG project retracts findings when the instrument turns out to be wrong. Better to say plainly: the instrument is built, the campaign is scheduled, here's what it will report once it runs.

This file will be updated with real numbers the day that first 24-hour campaign completes.
