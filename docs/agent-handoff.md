# Agent Handoff

## Current State

Repository foundation only. The system architecture, repository boundaries,
coding standard, testing philosophy, color-science terminology, and initial CI
scaffold are defined. No production firmware or hardware design is implemented.

## Active Goal

Choose and justify the first vertical slice for a basic tunable-white light before
freezing specific MCU, driver, emitter, or sensor parts.

## Accepted Constraints

- Local/offline operation is mandatory.
- OTA/FOTA and cloud/Internet dependencies are out of scope.
- BLE is the only planned wireless transport and remains optional for lamp operation.
- Wired programming/debug is the firmware update path.
- Basic builds must not require premium spectral sensing.
- Module names describe responsibilities rather than chosen part numbers.
- KISS/YAGNI and host-testable portable logic are project-wide rules.

## Open Decisions

- Controller MCU.
- LED-driver/power topology.
- Basic light-engine emitters and power target.
- Thermal/mechanical envelope.
- BLE implementation boundary.
- First sensor/measurement option and the metrics it can honestly support.
- Software/hardware licensing split before the first tagged implementation release.

## Known Issues

None. The repository intentionally contains no production target yet.

## Unverified Hardware Behavior

All electrical, optical, thermal, RF, and timing behavior remains unverified
because no hardware revision has been designed or built.

## Next Exact Step

Define requirements and trade-offs for the Phase 1 basic tunable-white vertical
slice, then record the first hardware-selection ADR only after that comparison is
complete.
