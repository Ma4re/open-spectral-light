# Agent Handoff

## Current State

Repository foundation only. The system architecture, repository boundaries,
coding standard, testing philosophy, color-science terminology/measurement
policy, and initial CI scaffold are defined. The first source-backed theory
foundation covers radiometry/photometry, spectral power distributions, core
colorimetry, and LED emission behavior. Phase 1 product requirements define the
tunable-white key/fill-light use case and an upper engineering target of 1200 lx
at 2 m through the large reference-modifier scenario. A first light-engine study
finds that this limit case is a several-hundred-watt-class problem and recommends
a symmetric tunable-white COB cluster as the first prototype direction while
preserving expansion capacity for auxiliary spectral channels. No production
firmware or hardware design is implemented.

## Active Goal

Turn the Phase 1 product requirements into quantitative optical, temporal,
electrical, and thermal targets before freezing specific MCU, driver, emitter,
connector, or sensor parts.

## Accepted Constraints

- Standalone local operation is the minimum product path; BLE control is optional.
- Basic builds do not require premium spectral sensing.
- The base light must already provide useful photographic/video light quality; premium modules add capability rather than repair a weak base product.
- Primary use is key/fill lighting for portrait, close/medium shots, and music-video scenes at approximately 1–2 m.
- Bowens S-mount is the modifier interface.
- Mains AC conversion remains external to the lighting head; the head accepts a defined external DC input.
- Normal-speed video compatibility through 60 fps is required, but shutter/exposure interaction must also be validated.
- Module names describe responsibilities rather than chosen part numbers.
- KISS/YAGNI and host-testable portable logic are project-wide rules.
- Theory documents explain scientific principles and design implications; they do not freeze implementation choices.

## Open Decisions

- Whether the 1200 lx at 2 m limit target remains practical after real modifier, thermal, acoustic, and cost validation.
- Final CCT range after emitter/channel trade-off analysis; 2700–6500 K is the current target.
- Emitter/channel architecture and future tint/spectral-expansion path.
- Temporal-modulation/driver strategy and camera shutter validation matrix.
- Electrical power requirement, DC input voltage, connector, and protection strategy.
- Thermal/mechanical envelope, fan requirement, and acoustic target.
- Exact local UI parameter set and whether the encoder needs supporting buttons.
- Controller MCU.
- BLE implementation boundary.
- First sensor/measurement option and the metrics it can honestly support.
- Software/hardware licensing split before the first tagged implementation release.

## Known Issues

None. The repository intentionally contains no production target yet.

## Unverified Hardware Behavior

All electrical, optical, thermal, RF, and timing behavior remains unverified
because no hardware revision has been designed or built.

## Next Exact Step

Quantify candidate tunable-white COB cluster sizes and electrical/thermal power
against the 1200 lx limit target, then define the first bench characterization
plan for modifier loss, CCT/Duv path, thermal drift, and output. Complete the
temporal-modulation and camera-interaction theory before freezing the driver
topology.
