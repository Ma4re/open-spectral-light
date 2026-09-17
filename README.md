# OpenSpectralLight

OpenSpectralLight is a modular open-development lighting platform for photography,
video, and color-science experimentation.

The project is designed so a useful light can be built without premium modules.
A basic build may use only the controller, power stage, and a tunable-white light
engine; spectral sensing, additional emitter channels, BLE, calibration tooling,
and advanced characterization are added only when they earn their cost and
complexity.

## Design principles

- **Standalone first.** The light is fully usable from local controls; BLE is an optional local control and telemetry interface.
- **Modular hardware.** Controller, power stage, light engine, sensing, optics, and mechanics have explicit boundaries.
- **Portable application logic.** Hardware-independent control and color logic is host-testable.
- **Measured claims.** CCT, Duv, flicker, CRI, TM-30, SSI, and related metrics are labeled according to whether they are measured, estimated, or calibrated.
- **YAGNI.** Features and abstractions are added when a real use case requires them, not in anticipation of one.

## Repository map

- `firmware/` — embedded firmware and its product boundaries.
- `hardware/` — electronics organized by physical module and revision.
- `optics/` — optical mixing, diffusion, reflectors, and optical experiments.
- `mechanical/` — enclosure, cooling, mounts, and mechanical interfaces.
- `software/` — optional host/user software such as calibration or control tools.
- `tools/` — developer-only scripts and utilities.
- `tests/` — system, characterization, and HIL assets that span firmware modules.
- `docs/` — architecture, engineering standards, color-science definitions, ADRs, and roadmap.

Start with [`docs/00-project-overview.md`](docs/00-project-overview.md) and
[`docs/01-system-architecture.md`](docs/01-system-architecture.md).

## Current status

The repository is at the architecture-foundation stage. No MCU, LED-driver IC,
spectral sensor, emitter family, mechanical envelope, or wireless implementation
is frozen yet.

## Build scaffold

The current CMake project intentionally contains no production targets. It exists
to establish reproducible host presets and CI before the first portable firmware
slice lands.

```sh
cmake --preset host-debug
cmake --build --preset host-debug
ctest --preset host-debug --output-on-failure
```

## Licensing

The source is being developed in public with the intent to be open source/open
hardware. The software and hardware license split will be frozen before the first
tagged implementation release; until license files are added, do not assume reuse
rights beyond those granted by applicable law.
