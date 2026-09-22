# Agent Handoff

## Current State

Repository foundation only. The system architecture, repository boundaries,
coding standard, testing philosophy, color-science terminology/measurement
policy, and initial CI scaffold are defined. The first source-backed theory
foundation covers radiometry/photometry, spectral power distributions, core
colorimetry, LED emission behavior, temporal light modulation, and camera
interaction. Phase 1 product requirements define the
tunable-white key/fill-light use case and an upper engineering target of 1200 lx
at 2 m through the large reference-modifier scenario. Power-envelope work
confirms that this limit case is a several-hundred-watt-class problem. Yujileds
high-power matrices are no longer a primary path because procurement and support
depend on direct manufacturer response. The preferred prototype direction is now
a distributor-backed modular light engine using standard warm/cool COB banks,
with Bridgelux Thrive split-white as the high-fidelity research direction and
Bridgelux Vesta tunable-white as an integrated-mixing reference. A reproducible bench characterization plan and temporal driver requirements are
now defined. The preferred temporal strategy is concurrent WW/CW current control
with continuous-current dimming across the principal video range and validated
synchronized PWM/hybrid control only if needed for deep dimming. No emitter or
driver part number is frozen and no production firmware or hardware design is
implemented.

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
- Normal-speed video compatibility through 60 fps is required using the 24/25/30/50/60 fps 180-degree-equivalent shutter baseline; faster shutters are characterization points.
- Normal CCT control shall use concurrent spectral mixing rather than alternating WW/CW time-division as the default mechanism.
- Any PWM/hybrid dimming region must be optically measured and camera-validated; PWM frequency alone is not proof of camera compatibility.
- The primary emitter path must be prototype-purchasable through authorized distribution without depending on manufacturer sales response.
- Direct-vendor-only custom LED matrices may be benchmarks but not the sole base light-engine source.
- Module names describe responsibilities rather than chosen part numbers.
- KISS/YAGNI and host-testable portable logic are project-wide rules.
- Theory documents explain scientific principles and design implications; they do not freeze implementation choices.

## Open Decisions

- Whether the 1200 lx at 2 m limit target remains practical after real modifier, thermal, acoustic, and cost validation.
- Final CCT range after emitter/channel trade-off analysis; 2700–6500 K is the current target.
- Exact distributor-backed emitter count/layout and future tint/spectral-expansion path.
- Driver power-stage/current-regulation topology and the minimum continuous-current dimming point.
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

Dimension a distributor-backed ~250–350 W split-white prototype using standard
warm/cool COBs, then compare its source area, current requirements, expected
flux, and mixing geometry against an integrated Vesta reference. Prepare the
oversized thermal bench fixture in parallel. Do not freeze the driver until the
selected emitter bank voltage/current envelope is established.
