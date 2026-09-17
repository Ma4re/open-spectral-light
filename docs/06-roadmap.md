# Roadmap

The roadmap is intentionally capability-driven rather than part-number-driven.
Each phase should leave a useful, testable artifact.

## Phase 0 — Repository foundation

- Architecture and repository boundaries.
- Coding/testing standards.
- Host CMake/CI baseline.
- No hardware part selections frozen.

## Phase 1 — Basic tunable-white light

- Choose controller MCU and power architecture.
- Implement two independently controlled white channels.
- Manual/local controls and wired programming/debug.
- Basic current, temperature, and fault protection.
- Characterize dimming, flicker, thermal behavior, and usable optical output.

## Phase 2 — Local professional control

- Add BLE if it materially improves operation.
- Define a compact local control/telemetry protocol.
- Improve dimming/flicker behavior and thermal compensation.
- Preserve full offline/local operation without a phone.

## Phase 3 — Measurement module

- Select an affordable sensor path based on metrics actually needed.
- Add relative/estimated CCT and Duv feedback with documented calibration limits.
- Add dedicated flicker measurement if the basic driver telemetry is insufficient.

## Phase 4 — Spectral light engine

- Add only the spectral correction channels justified by measured deficiencies.
- Build channel characterization datasets.
- Implement bounded spectral optimization under power/thermal constraints.

## Phase 5 — Calibration and advanced characterization

- Optional host calibration tooling.
- Reference-instrument workflow.
- CRI/TM-30/SSI only where sensor/reference quality supports defensible results.

## Persisting non-goals

No phase implicitly adds cloud infrastructure, Wi-Fi dependence, OTA/FOTA, or an
Internet requirement.
