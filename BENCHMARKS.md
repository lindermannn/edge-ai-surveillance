# Numen Vision — benchmark record and limits

This is a public summary of the private implementation's `docs/BENCHMARK_RESULTS.md`, `docs/M6_INTEGRATION.md` and `docs/EDGE_CAPACITY_3_CAMERAS.md` as reviewed on 2026-09-23. The raw video, runtime logs and deployment configuration are not part of this repository. Results below are measurements on an RK3576 development board, not a certification or a field pilot.

## Recorded M7 run

On 2026-09-18, the private harness ran for **7.85 continuous hours** (471 one-minute samples). Four replayed 4 MP H.265 sources at 15 input fps were decoded and scaled to 960×540; analysis was configured at 4 fps per camera with rules and event evidence enabled. All four inputs replayed the same file. The board was open on a desk, without an enclosure.

| Criterion | Acceptance threshold | Recorded |
|---|---:|---:|
| Analysed fps per camera | ≥ 3.8 (95% of 4) | 3.84 on all four |
| Restarts, vision errors, source reconnections | 0 each | 0 each |
| Thermal throttling | None | None; 46.2 °C peak |
| Memory slope after warm-up | < 10 MB/hour | +7.5 MB/hour |
| Process-tree memory after warm-up | Recorded, not a standalone pass criterion | 457 MB minimum; 512 MB maximum |

The harness recorded 443 events in this run. It checks operation and resource use; this event count does **not** establish detection precision or alert usefulness. Component inference timings from shorter M6 runs are not camera-to-alert latency.

## What remains unmeasured

- **24-hour endurance:** this run lasted 7.85 hours. A longer window is assigned to the pilot.
- **Physical cameras and enclosure:** input was file replay; the 4 MP material was upscaled from 640×360, and the board was in open air. Decode cost and thermals can differ on site.
- **Full deployed load:** continuous disk recording, go2rtc live view and the dashboard were not loaded during this run.
- **Connectivity failure on the board:** the durable outbox has automated tests, but cloud loss was not exercised in this run.
- **Detection and alert accuracy:** requires annotated real camera data in a residential pilot.
- **Earlier tender targets:** <200 ms glass-to-glass and four concurrent streams at 15+ analysed fps were design targets, not results. They do not describe the current residential operating point.

The defensible operating claim today is **four replayed sources at about 4 analysed fps each for 7.85 hours under the conditions above**. No public raw dataset or replication package is offered because the implementation and source media are private.
