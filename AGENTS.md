# OpenSpectralLight Agent Instructions

## Sources of truth

- Git and the repository contents are the source of truth for implementation state.
- `docs/agent-handoff.md` records current intent, verified state, risks, and the next exact step.
- Architecture documents define subsystem responsibilities and dependency boundaries.
- `docs/03-coding-standards.md` is the implementation contract for project-owned firmware.
- ADRs record accepted durable decisions; do not silently contradict them in code.

## Before work

1. Read `docs/agent-handoff.md` and the documents governing the affected subsystem.
2. Inspect repository status and recent history before editing.
3. Preserve unrelated work and keep one vertical slice per branch/commit series.
4. Do not freeze an MCU, LED driver, sensor, emitter, protocol, or mechanical interface by inference.
5. For firmware changes, read `docs/03-coding-standards.md` first.

## Scope and simplicity

- KISS and YAGNI are mandatory engineering constraints.
- Prefer concrete code over frameworks until a second implementation or a real test seam exists.
- Do not add folders, interfaces, services, buses, configuration layers, or dependencies for hypothetical future use.
- Shared runtime code is extracted only after at least two real consumers need the same behavior.
- The base light must not require a phone, Internet access, a cloud service, or a spectral sensor to operate.
- OTA/FOTA is out of scope. Firmware updates use wired programming/debug tooling.
- BLE is the only planned wireless transport and is local-only; its exact implementation is not frozen.

## Firmware boundaries

- `App` owns portable product behavior and algorithms. It must not include vendor HAL, RTOS, pin, register, or board headers.
- `Interfaces` contains only boundaries that are genuinely required for hardware isolation or testing.
- `Drivers` owns external-device protocols and vendor-event normalization, not product policy.
- `BSP` owns MCU/board-specific startup, pins, clocks, interrupts, ADC/timer setup, and vendor HAL integration.
- Hardware-dependent facts must enter portable code through explicit values or interfaces.

## Development and verification

- New portable behavior is test-first: observe RED for the missing behavior, implement the minimum change, then refactor under green tests.
- Tests are deterministic: no sleeps, wall-clock dependence, uncontrolled randomness, network dependence, or developer-machine paths.
- Important negative/error paths receive tests when they affect output, power, thermal behavior, persistence, or user-visible control.
- A successful host build/test never proves optical output, flicker, thermal limits, electrical protection, RF behavior, or target timing.
- Hardware-dependent behavior that remains unverified is stated explicitly in the handoff/PR.

## Hardware changes

Treat pin assignments, current limits, power budgets, driver topology, clocking,
ADC scaling, protection thresholds, thermal limits, PCB interfaces, emitter
strings, and connector pinouts as hardware contracts. Do not guess them. If a
required fact is not documented or established by primary-source evidence, stop
and document the exact decision needed.

## Definition of done

A slice is complete only when applicable builds/tests pass, the final diff is
limited to its goal, documentation matches the resulting behavior, and any
unverified physical behavior is identified. Agents may create branches/PRs or
modify the remote repository only when the user explicitly authorizes that
external action; merging remains a separate explicit decision.
