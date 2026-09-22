# Documentation

The core documentation is deliberately small and architectural. It explains what
the system is, where responsibilities live, what engineering rules apply, and
which decisions are actually frozen.

## Core project documents

Read these in order:

1. `00-project-overview.md`
2. `01-system-architecture.md`
3. `02-repository-organization.md`
4. `03-coding-standards.md`
5. `04-testing-and-validation.md`
6. `05-color-science.md`
7. `06-roadmap.md`

`05-color-science.md` defines project terminology and measurement-claim policy.
It is intentionally concise and should not grow into a color-science textbook.

## Product requirements

Product requirements live under `requirements/`. They describe intended use,
accepted constraints, engineering targets, and deliberately open parameters
without prematurely freezing implementation details.

Current requirement set:

- [`requirements/phase-1-tunable-white.md`](requirements/phase-1-tunable-white.md)

Requirement states are `FROZEN`, `TARGET`, and `OPEN` so unresolved values can be
documented without being mistaken for final specifications.

## Theory foundations

Source-backed scientific background lives under `theory/`. These documents
explain the physical principles behind later requirements, component selection,
calibration, and verification without freezing implementation choices.

Recommended starting order:

1. [`theory/radiometry-photometry.md`](theory/radiometry-photometry.md)
2. [`theory/spectral-power-distribution.md`](theory/spectral-power-distribution.md)
3. [`theory/color-science.md`](theory/color-science.md)
4. [`theory/led-emission-behavior.md`](theory/led-emission-behavior.md)
5. [`theory/temporal-light-modulation.md`](theory/temporal-light-modulation.md)
6. [`theory/camera-interaction.md`](theory/camera-interaction.md)

Additional theory chapters should be added only when they support a real design,
measurement, or verification need.

## Engineering studies

Pre-decision engineering comparisons live under `studies/`. They may recommend
a prototype direction, but they do not freeze hardware. Durable accepted choices
still require an ADR.

Current studies:

- [`studies/phase-1-light-engine.md`](studies/phase-1-light-engine.md)
- [`studies/phase-1-power-envelope.md`](studies/phase-1-power-envelope.md)
- [`studies/phase-1-emitter-procurement.md`](studies/phase-1-emitter-procurement.md)
- [`studies/phase-1-bench-characterization.md`](studies/phase-1-bench-characterization.md)
- [`studies/phase-1-temporal-driver-requirements.md`](studies/phase-1-temporal-driver-requirements.md)
- [`studies/light-engine-module-interface.md`](studies/light-engine-module-interface.md)
- [`studies/prototype-a-thrive-light-engine.md`](studies/prototype-a-thrive-light-engine.md)
- [`studies/phase-1-power-stage-architecture.md`](studies/phase-1-power-stage-architecture.md)

Accepted durable decisions belong in `adr/`. Current execution context belongs in
`agent-handoff.md`. Temporary implementation plans do not override architecture,
project policy, product requirements, or accepted ADRs.
