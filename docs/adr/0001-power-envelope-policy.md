# ADR-0001: Separate useful output power from hardware fault limits

Status: Accepted
Date: 2026-10-08

## Context

Prototype A has two logical tunable-white roles and eight individually regulated
Bridgelux V18-class COB branches (four WW, four CW). Earlier studies used
approximately **300 W total LED electrical output** as an initial sizing and
characterization point, with a regulated **48 V** input and an approximately
**400 W PSU** as the first example source class.

Those estimates are **not a measured photometric optimum, a fixed maximum
output specification, or a complete protection design**. Eight branches near
75 W each could demand about 600 W if enabled together, regardless of what a
400 W-class source can actually sustain.

The Phase 1 objective is useful, spectrally consistent and camera-safe light
through the target modifiers, not an arbitrary electrical wattage.

## Decision

1. **Do not impose a 300 W fixed hardware clamp.** Approximately 300 W remains
   an initial bench/thermal sizing point and performance comparison baseline.
2. The actual **qualified continuous operating LED-power envelope** shall be
   established by measured optical benefit, emitter limits, the LEM thermal
   design, cooling/acoustic behavior, qualified PSM ratings, the connected
   source and cabling, and environmental derating. It may be **greater than
   300 W** when evidence justifies the additional light and hardware costs.
3. The Controller Module (CM) performs **normal aggregate WW+CW power
   allocation** within the current qualified envelope. This is not a safety
   certification or an independent hardware protection path.
4. **Independent electrical safeguards are mandatory**: bounded per-branch
   current, default-off output enable/fault response, safe response to
   temperature/LEM errors and controller loss, and PSM/source input overload
   containment. Their thresholds and response times follow actual qualified
   electrical/thermal limits; no arbitrary 300 W trip is required.
5. The connected power supply must support the **selected operating
   envelope**, including converter losses, auxiliaries, cable loss and
   derating. The approximately 400 W PSU class is only an initial 300 W-class
   bench/source option. Higher-qualified output can require a larger PSU,
   input connector/cable and revised cooling.
6. The selected LED's published maximum **2.34 A** is a component limit, not a
   preferred running point. The roughly **2.11 A** per-branch value remains the
   initial branch design/characterization point; any higher current must be
   explicitly qualified and remain within all manufacturer limits.
7. If a more powerful mode brings little useful illumination improvement
   after modifier losses, or disproportionate thermal/noise/cost penalties, do
   not add power merely because electrical headroom exists.

The modular CM/PSM/LEM contract and the two-channel concurrent spectral mix
remain unchanged. A future higher-power implementation can qualify a larger
PSM/source class without imposing a new emitter vendor on the LEM interface.

## Alternatives considered

- **Hardwired 300 W output clamp:** rejected as an arbitrary bottleneck. It
  would not by itself provide per-COB fault protection, controller-loss
  containment, or source overvoltage protection.
- **Unbounded output until a PSU trips:** rejected. The PSU's own
  overcurrent/hiccup is not a substitute for a product-level operating budget
  and independent hazardous-overload containment.
- **Set a new higher number (e.g. 400/500/600 W) today:** rejected. No
  optical, thermal, acoustic, electrical and fault-injection evidence exists
  to justify a fixed maximum.

## Consequences

- Requirements and studies shall call ~300 W the *initial characterization
  point*, not a total LEM hardware ceiling.
- Firmware shall accept a **qualified** power budget rather than compile-time
  assumptions tied to one COB family or one 400 W PSU; the exact source
  identification/selection mechanism remains OPEN.
- The physical input protection and branch-safe states remain obligatory even
  when the qualified normal output is increased.
- The ~1200 lx through the 120 cm double-diffusion plus grid modifier at 2 m
  remains a photometric TARGET; neither 300 W nor a higher electrical figure
  guarantees it.

## Verification / evidence

Before a higher continuous-power rating is claimed, record:

- per-branch current/voltage and worst-case component ratings;
- LED output power, PSM input power and efficiency (measured, not inferred);
- validated source current/voltage capability, inrush and protection behavior;
- COB case and PSM hotspot temperature to thermal stabilization at qualified
  ambient conditions, with fan/acoustic characterization;
- optical lux through actual modifiers, SPD/CCT/Duv and spatial uniformity;
- input overload, stuck-on command, fault output, enable/freshness and
  thermal-sensor fault injection;
- operational derating and the effective limits for each approved LEM/PSM/source
  combination.

This is an accepted architectural **policy**, not evidence that a higher
output-power mode has already been qualified.

## Related documents

- [Phase 1 requirements](../requirements/phase-1-tunable-white.md)
- [Safety contract](../studies/prototype-a-power-and-fault-safety.md)
- [Prototype A light engine](../studies/prototype-a-thrive-light-engine.md)
- [Power-stage architecture](../studies/phase-1-power-stage-architecture.md)
