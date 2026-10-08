# Documentation Sanity Check — 2026-10-08

## Scope and method

Scope: all 48 Markdown files in the current OpenSpectralLight repository file
inventory, across root governance documents, architecture, requirements, theory,
studies, historical plans/specs, and module READMEs. The complete repository
inventory contains 55 changed/added files relative to the initial commit.

The review checked:

- alignment of governance, basic product requirements, architecture and roadmap;
- FROZEN/TARGET/OPEN boundaries and whether prototype data are represented as
  verified specifications;
- LEM/PSM modularity and fail-safe power limits;
- emitter datasheet family identity and selected COB operating envelope;
- major analytical electrical calculations and assumptions;
- whether older vendor and driver studies are clearly historical;
- internal Markdown relative links and code-fence balance;
- selected primary references (TI, Bridgelux, CIE/IES).

**Limits:** This is a documentation review. It is not a SPICE model execution,
physical electrical/optical/thermal validation, exhaustive third-party URL
availability scan, or manufacturing design release. No claim of hardware safety
or 60-fps flicker performance is established here.

## Findings requiring action before Rev A hardware

### High — Aggregate power/fault containment

Eight branches rated around 75 W each represent about 600 W of installed
individual capacity. The Phase 1 LEM budget is approximately 300 W, and the
candidate external power source is only about 400 W.

Normal WW/CW budgeting and independent hardware/source-level fault response must
be specified separately. Eight independent constant-current regulators alone do
not guarantee the total system power ceiling.

**Status:** requirement and explicit study warnings added; actual circuit,
thresholds, testing and fault-response implementation remain OPEN.

### High — Internal switch OCP is not emitter protection

Prototype A's nominal 2.11 A setpoint and emitter maximum 2.34 A are far below
the TPS922054/055 switch-cycle current protection level. Its internal switch OCP
must not be described as a hard 2.34 A COB limit.

**Status:** study corrected; independent branch setpoint/fault containment,
startup/transient behavior, and sensor-fault response must be established.

### High — Thermal sensing coverage

One representative sensor per WW/CW bank helps cover asymmetric CCT loading but
may miss one COB with a defective thermal interface or mounting condition.

**Status:** coverage limitation added to bench plan and handoff; final
temperature-sensor placement and protective response remain OPEN.

## Findings corrected in documentation

### Medium — Output capacitance estimate

The 0.9 Ohm COB dynamic-resistance assumption was not adequately justified.
Typical Bridgelux V18 Vf data near the intended operating current imply a DC
secant slope of roughly 1.7 Ohm between 1.755 A and 2.34 A, which itself does
not prove small-signal impedance at switching frequency.

The earlier COUT sizing table was withdrawn; 3–5 uF effective remains a **test
candidate**, not a guaranteed low-ripple solution.

### Medium — Optical/thermal feasibility

A ~110 mm octagonal COB source and a smaller Bowens-compatible output aperture
create uncertain mixing losses, angular color uniformity, and thermal density.
The 1200 lx / 2 m / 120 cm double-diffusion-and-grid performance remains a
TARGET. External fixture interpolations are illustrative, not a transfer
function for this light engine.

No design promise should be made before modifier measurements.

### Medium — Module nonvolatile identity and calibration

The accepted LEM requirement now explicitly requires nonvolatile identity and
calibration metadata. The memory bus, part, storage format and update method
remain OPEN. Invalid, unverified, or missing descriptors must not permit
normal LED operation, and the module metadata cannot override physical PSM
limits.

### Low — Historical studies versus current baseline

- Yujileds matrix data are now explicitly archived benchmarks.
- Prototype A is explicitly the high-fidelity **split-white** option; integrated
  tunable-white clusters are the alternative optical reference.
- LM3409HV remains a valid fallback/reference, not the current implementation.
- TPS922054 400 kHz / 47 uH is a *preferred analytical branch*, not a validated
  PCB or frozen manufacturing design.
- V18 Thrive **CRI 95** shall not inherit the Vesta Thrive **CRI 98** marketing
  specifications.
- Phase 1 Basic must already satisfy its agreed normal-camera temporal baseline;
  later capability tiers extend rather than repair core video behavior.

## Structural result

The 48 Markdown files were checked for relative Markdown links against the
repository file inventory. **No broken relative links were found.**

No unbalanced triple-backtick code fences were found.

This does not test every external link or render every formula on GitHub.

## What is still OPEN

1. Controller reset/disconnect, LEM invalid-metadata, NTC fault, FAULT assertion,
   short/open COB and power-overrequest response matrix.
2. PSM aggregate power limiting and independently enforced external input limits.
3. TPS92205x macro-model and one-branch hardware validation at 31/35.5/41.2 V,
   input-voltage corners, startup, COUT, dimming and thermal conditions.
4. Actual input supply limits, fusing, transient protection and safe connectors.
5. Measured COB electrical/spectral behavior and local optical-mixing losses.
6. LEM mechanical/thermal datum and mounting torque/contact requirements.
7. Optically measured camera banding/flicker and CCT/Duv/spatial uniformity.
8. Open-source software/hardware licensing before the first tagged release.

## Decision

Documentation is consistent enough to proceed to **protected one-branch
simulation and characterization planning**, but **not** to freeze or release the
eight-branch PSM hardware.

The aggregate-power and per-COB overcurrent fault paths are the first design
gates to resolve. The project's modular CM/PSM/LEM architecture does not need to
be discarded.

## Primary references sampled

- TI TPS922054/TPS922055:
  https://www.ti.com/product/TPS922054
- Bridgelux Gen 7 V18 Thrive DS322:
  https://www.bridgelux.com/thrive
- CIE S 025/E:2015:
  https://www.cie.co.at/publications/test-method-led-lamps-led-luminaires-and-led-modules
- ANSI/IES LM-79-24:
  https://store.ies.org/product/optical-and-electrical-measurements-of-solid-state-lighting-products/
