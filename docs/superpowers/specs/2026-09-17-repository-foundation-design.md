# Repository Foundation Design

## Goal

Create a small but durable monorepo foundation for OpenSpectralLight that can grow
from a basic tunable-white lamp into an optional spectral/measurement platform
without making premium features mandatory.

## Decisions

- Use one monorepo for firmware, electronics, optics, mechanics, optional host software, tools, system/HIL tests, and architecture documentation.
- Organize modules by responsibility, never by currently considered part number.
- Establish explicit architecture boundaries, coding standards, deterministic host tests, honest HIL evidence, ADRs, CMake presets, formatting/static-analysis configuration, and an agent handoff.
- Keep standalone local operation as the minimum product path, with BLE available as an optional local control and telemetry transport.
- Keep the initial build system host-only and MCU-neutral until the controller MCU is selected.
- Do not create empty implementation layers or speculative shared/common code.

## Firmware architecture

Portable product behavior belongs in `App`; concrete external ICs in `Drivers`;
MCU/board integration in `BSP`; interfaces exist only where hardware isolation or
tests genuinely require them. No RTOS or middleware layer is created at
foundation stage.

## Verification architecture

Host tests prove deterministic software behavior. Target/HIL evidence is required
for electrical, optical, thermal, RF, flicker, and real-time claims. CI at the
foundation stage only configures/builds the neutral CMake project and executes the
current CTest set.

## Repository growth rule

A directory, abstraction, dependency, or service appears when a real implemented
slice needs it. Future capability is enabled by stable boundaries, not by
pre-creating its implementation.
