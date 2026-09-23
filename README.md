# Numen Vision — Edge Video Analytics

Numen Vision began as a multi-camera prototype for public-sector surveillance tenders and evolved into a residential edge appliance for entrances, yards and gates. The implementation is private. This repository documents the architecture, engineering decisions and measured limits without publishing camera data or deployment configuration.

## Current system

The prototype first ran against live RTSP streams on x86/Windows. The current implementation also runs on a NanoPi M5 with a Rockchip RK3576: FFmpeg uses the VPU for H.265 decode and scaling, and YOLOX-Nano FP16 runs through RKNN on the NPU. Per-camera rules turn detections into events; snapshots and short clips are retained when a rule fires. Events can cross the network, while continuous video stays on site.

```text
IP camera / replayed source
  → VPU decode and scale → NPU detection → tracking and rules
  → local evidence and durable event queue → cloud alert
```

The earlier x86 prototype and the RK3576 implementation are stages of the same product. The board measurements below describe the residential configuration, not the earlier tender target of four streams at 15 analysed fps or under 200 ms glass-to-glass latency.

## Measured on RK3576

In a recorded **7.85-hour run on 2026-09-18**, four 4 MP H.265 sources at 15 input fps were replayed from the same file, decoded and scaled to 960×540, then analysed at a configured 4 fps per camera. Real rules and event evidence were enabled. The board was open on a desk.

| Measure | Recorded result |
|---|---:|
| Analysed frames | 3.84 fps per camera (96% of the 4 fps setting) |
| Restarts / vision errors / source reconnections | 0 / 0 / 0 |
| Peak board temperature | 46.2 °C; no throttling recorded |
| Memory after warm-up | 457–512 MB RSS; +7.5 MB/hour slope |

These results come from the private implementation's M7 benchmark log. [Benchmarks and limitations](BENCHMARKS.md) describes the measurement procedure and what has not been validated. The recorded result supports **four replayed sources at about 4 analysed fps each under the tested conditions**; it is not a field reliability or detection-accuracy claim.

## Engineering decisions

- **Inference on the device.** Video is decoded and analysed locally. The cloud receives structured events and selected evidence, not a continuous feed.
- **Hardware-specific acceleration.** FFmpeg uses `rkmpp`/RGA for decode and scaling; the detector is compiled for the Rockchip NPU through RKNN.
- **Rules after tracking.** Zone and line-crossing decisions use object identities to avoid repeated alerts from consecutive frames.
- **Offline continuity.** A durable outbox queues events until connectivity returns. Its behavior is covered by tests in the private implementation; a board-level cloud outage run is still pending.

## Current limits and next validation

No 24-hour campaign, physical-camera run, enclosed-device thermal test or residential pilot has been completed. The four replayed inputs used the same upscaled source; continuous recording and the live dashboard were not loaded in the benchmark. Alert accuracy must be evaluated with real pilot data. The earlier targets of **<200 ms glass-to-glass** and **four streams at 15+ analysed fps** have not been measured and are not claimed here.

The next validation is a pilot with physical cameras, continuous recording, network interruption and long-duration operation. The source code, model build, deployment configuration and tender material remain private; the architecture and measurement boundaries are available for technical review.
