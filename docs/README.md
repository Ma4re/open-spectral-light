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

## Theory foundations

Source-backed scientific background lives under `theory/`. These documents
explain the physical principles behind later requirements, component selection,
calibration, and verification without freezing implementation choices.

Recommended starting order:

1. [`theory/radiometry-photometry.md`](theory/radiometry-photometry.md)
2. [`theory/spectral-power-distribution.md`](theory/spectral-power-distribution.md)
3. [`theory/color-science.md`](theory/color-science.md)

Additional theory chapters should be added only when they support a real design,
measurement, or verification need.

Accepted durable decisions belong in `adr/`. Current execution context belongs in
`agent-handoff.md`. Temporary implementation plans do not override architecture,
project policy, or accepted ADRs.
