# Architecture Decision Records

Use an ADR for a durable choice that constrains future implementation: MCU family,
LED-driver topology, internal module interface, emitter/channel architecture,
dimming strategy, BLE protocol boundary, calibration-data format, or a comparable
cross-cutting decision.

Do not create ADRs for temporary experiments or choices that remain intentionally
open.

Number accepted ADRs sequentially:

```text
0001-controller-mcu.md
0002-led-driver-architecture.md
```

Recommended sections:

```text
# ADR-NNNN: Title

Status: Proposed | Accepted | Superseded
Date: YYYY-MM-DD

## Context
## Decision
## Alternatives considered
## Consequences
## Verification / evidence
```

Current accepted decisions:

- [ADR-0001 — Separate useful output power from hardware fault limits](0001-power-envelope-policy.md)
