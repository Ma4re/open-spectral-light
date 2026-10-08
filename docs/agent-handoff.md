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
Bridgelux Vesta tunable-white as an integrated-mixing reference. The light
engine is now explicitly a replaceable module: compatible emitter changes stay
behind stable mechanical/thermal/optical/power/identity interfaces, and a future
out-of-envelope engine may replace the power-stage module with it without
redesigning the controller. A reproducible bench characterization plan and temporal driver requirements are
now defined. The preferred temporal strategy is concurrent WW/CW current control
with continuous-current dimming across the principal video range and validated
synchronized PWM/hybrid control only if needed for deep dimming. Prototype A is
now dimensioned as 4 x 2700 K + 4 x 6500 K V18 Thrive COBs in an alternating
octagonal ring, with an **initial ~300 W total characterization point**, not
a fixed maximum, approximately 2.11 A baseline per active branch, and an
approximately 110 mm raw source envelope. [ADR-0001](adr/0001-power-envelope-policy.md)
accepts a higher qualified operating-power level if it produces meaningful
additional optical output without disproportionate thermal/acoustic costs. The Bridgelux parts are prototype references rather than frozen vendor
dependencies. Power-stage comparison still prefers eight independent buck constant-current
branches from a nominal 48 V bus, but the branch-controller baseline has moved
from LM3409HV to the newer TI TPS92205x 4 A family. TPS922054 (spread spectrum
disabled) is the first optical-characterization candidate; TPS922055 (spread
spectrum enabled) is the EMI comparison candidate on the same branch
architecture. TI's 48 V / 36 V / 2 A reference design closely matches Prototype
A. Detailed branch sizing now prefers TPS922054 in VSON DMT at 400 kHz with a
47 uH inductor, ~95 mOhm current sense, ~3–5 uF effective COUT, and a candidate
~46 V minimum full-power input. TPS922055 is the same-architecture spread-
spectrum EMI A/B variant; 600 kHz / 33 uH is retained only as a switching-
frequency comparison. LM3409HV remains an active, mature 75 V fallback/reference.
A ~400 W external source class is only the starting ~300 W-class **bench
supply** candidate; a higher qualified output mode requires a compatible,
higher-rated source and validated connectors/cooling as appropriate. Neither
48 V nor a driver IC is yet a frozen hardware contract.

## Active Goal

Translate the Phase 1 electrical and thermal targets into a **verifiable safe
one-branch prototype**, including independent source overload containment,
per-COB current limits, fail-off enable behavior, thermal fault response,
and module NVM validation, before selecting/finalizing PSM implementation
hardware.

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
- The light engine is service-replaceable behind a stable module interface; Phase 1 does not require hot-swap.
- Compatible LEM replacements must not require controller/UI redesign; an out-of-envelope LEM may be paired with a replacement power-stage module.
- Module names describe responsibilities rather than chosen part numbers.
- KISS/YAGNI and host-testable portable logic are project-wide rules.
- Theory documents explain scientific principles and design implications; they do not freeze implementation choices.

## Open Decisions

- Whether the 1200 lx at 2 m limit target remains practical after real modifier, thermal, acoustic, and cost validation.
- Final CCT range after emitter/channel trade-off analysis; 2700–6500 K is the current target.
- Final qualification of the Prototype A 4+4 layout, mixing geometry, and future tint/spectral-expansion path.
- Validation of the preferred eight-branch buck PSM, TPS922054-vs-TPS922055 choice, and minimum continuous-current dimming point.
- Final 48 V input tolerance, connector, and protection strategy.
- Thermal/mechanical envelope, fan requirement, and acoustic target.
- Exact local UI parameter set and whether the encoder needs supporting buttons.
- Controller MCU.
- BLE implementation boundary.
- First sensor/measurement option and the metrics it can honestly support.
- Software/hardware licensing split before the first tagged implementation release.

## Known Issues

The safety/fault response requirements and test matrix are now documented in
[`studies/prototype-a-power-and-fault-safety.md`](studies/prototype-a-power-and-fault-safety.md).
**This is a completed study, not an implemented or validated circuit.**

The 2026-10-08 documentation sanity check identified hardware-interface
validation gaps that must not be treated as resolved:

- Eight independent 75 W-class branches can together request ~600 W, beyond
  the first ~400 W-class source. The CM must enforce the **currently qualified**
  aggregate output allocation and the PSM must provide independent hazardous
  input-overload containment, without a hardwired 300 W limit.
- TPS92205x internal switch-cycle overcurrent protection is not a qualified
  2.34 A emitter-protection mechanism; validate current reference, analog clamp,
  startup, and sense-fault response.
- Prototype A COUT and optical ripple are only preliminary; a previous 0.9 Ohm
  LED dynamic-resistance assumption has been withdrawn in favor of measured
  small-signal behavior.
- Two bank-level temperature sensors may not detect a poor individual-COB
  thermal contact; verify thermal-contact and shutdown coverage during hardware
  design.

No production target or physical hardware test results exist yet.

## Unverified Hardware Behavior

All electrical, optical, thermal, RF, and timing behavior remains unverified
because no hardware revision has been designed or built.

## Next Exact Step

The CM/PSM/LEM logical safety contract and minimum protection-architecture
**comparison** are documented in
[`studies/prototype-a-minimal-psm-protection-architecture.md`](studies/prototype-a-minimal-psm-protection-architecture.md).
The leading **study direction, not a frozen schematic**, is a default-off EN/PWM
hardware gate with independent external window watchdog, shared FAULT latch,
PSM input protection with appropriately rated external switch FETs, and normal
branch-current bounding by TPS922054 R_SENSE. TI TPS3430/TPS3431 and Analog
Devices LTC4368 are *examples to evaluate*, not approved BOM selections.
One single-branch overcurrent failure can remain below the total PSM input
trip: the input protector alone cannot qualify the COB current-safety path.
**Resolved in ADR-0001:** do not hardwire a 300 W limit. Establish the
qualified useful continuous-power envelope through optical, thermal and
acoustic tests; design hardware fault limits from source/PSM/LEM electrical
ratings and risk analysis, not from a nominal characterization wattage.

Now propose the minimal **actual protection schematic** (watchdog/reset,
FAULT latch, EN/PWM dominant override, source protector with FET SOA/input
transient analysis, branch sense-fault transient), and simulate its testable
subcircuits before a one-branch TPS922054 PCB. Verify 31/35.5/41.2 V LED loads, ~2.11 A
nominal maximum command, safe transient current, startup, thermal/fault
response, COUT/dimming and the controller-disconnected condition. Only after
one branch and a representative aggregate-overload test pass should the branch
be replicated eight times. Continue the carrier thermal model and local mixing
study in parallel.
