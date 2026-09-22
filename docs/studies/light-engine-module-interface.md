# Light Engine Module Interface Study

## 1. Purpose

OpenSpectralLight should be able to replace an emitter family without forcing a
redesign of the whole product.

The objective is therefore not to standardize one COB or one LED matrix. It is
to standardize the **interfaces around the light engine** so that different
emitter implementations can remain compatible with the same head architecture.

The proposed module names are:

- **Controller Module (CM)** — product behavior, UI, calibration logic, thermal
  policy, diagnostics, optional BLE;
- **Power Stage Module (PSM)** — regulated LED-current channels, electrical
  protection, power telemetry, current actuation;
- **Light Engine Module (LEM)** — emitters, emitter carrier/MCPCB, local heat
  spreader, temperature sensing, module identity/calibration data, and local
  optical mixing elements where required.

The stable architectural chain is:

```text
Controls / optional BLE
          |
          v
 +-------------------+
 | Controller Module |
 +---------+---------+
           |
     control/telemetry
           |
           v
 +-------------------+
 | Power Stage Module|
 +---------+---------+
           |
   regulated LED power
           |
           v
 +-------------------+
 | Light Engine Module|
 +---------+---------+
           |
  optical interface
           |
           v
   Bowens/mixing optics
```

This study defines the logical contracts. Exact connector families, bolt
patterns, voltage/current limits, and dimensions remain OPEN until the first
250–350 W prototype is dimensioned.

## 2. Why the power stage remains a separate module

Making the LEM replaceable does not mean one driver must support every possible
LED ever made.

That would create unnecessary cost and complexity.

Instead, compatibility is layered:

### Level 1 — LEM replacement inside one electrical envelope

If a replacement light engine:

- uses the same number of current channels;
- remains within the PSM voltage/current/power envelope;
- satisfies the same sensing/interface contract;

then only the LEM changes.

The controller, UI, firmware product behavior, enclosure, and power stage remain
unchanged.

### Level 2 — LEM requires a different electrical envelope

If a future light engine requires substantially different:

- forward voltage;
- current;
- channel count;
- power-stage topology;

then the **PSM and LEM may change together**.

The controller and higher-level product remain unchanged.

This is the key modularity rule. The controller shall not be designed around a
specific COB electrical model.

## 3. Interface model

The Light Engine Module has five external contracts:

1. mechanical;
2. thermal;
3. optical;
4. high-power electrical;
5. low-voltage identification/sensing.

This mirrors the general interoperability philosophy used by Zhaga, which
standardizes interfaces around LED modules rather than the internal emitter
technology.

OpenSpectralLight is not adopting a Zhaga form factor. Existing Zhaga Books are
primarily targeted at other luminaire classes and do not directly match a
compact several-hundred-watt Bowens light. The project instead adopts the same
separation-of-concerns principle.

## 4. Mechanical interface

The head shall define a stable **LEM mounting datum system**.

At minimum the eventual mechanical interface must define:

- optical-axis reference;
- rear thermal-interface plane;
- mounting-hole pattern;
- allowed module outer envelope;
- connector/keep-out regions;
- maximum module mass;
- insertion orientation / poka-yoke feature.

The exact dimensions remain OPEN until the first split-white prototype is laid
out.

The LEM mounting pattern should not follow the mounting holes of any one COB.
Vendor-specific COB mounting is internal to the LEM.

The intended physical stack is:

```text
emitters / local optics
        |
emitter carrier
        |
local heat spreader
        |
======================  <- standardized LEM thermal plane
thermal interface
======================  <- head cold plate / heatsink
        |
cooling system
```

A future emitter can therefore use completely different COB footprints while
presenting the same outer LEM interface to the head.

## 5. Thermal interface

The LEM should own the difficult vendor-specific thermal transition from the
emitters to a common module heat-spreader surface.

The head should own:

- bulk heatsink;
- heat pipes/vapor chamber if used;
- fan and ducting;
- enclosure airflow;
- ambient temperature sensing.

The LEM should own:

- emitter-to-carrier thermal path;
- carrier-to-local-spreader path;
- module temperature sensor(s);
- declared thermal limits for that module.

The standardized thermal boundary is the rear LEM thermal plane.

Future revisions must define:

- contact area;
- flatness;
- surface-finish expectations;
- allowable interface thermal resistance;
- mounting force/torque;
- permitted TIM type/thickness;
- maximum heat flow for each compatibility class.

These values shall be based on the first real prototype rather than guessed now.

## 6. Optical interface

The Bowens S mount belongs to the **head**, not to one emitter module.

The LEM optical contract should define:

- optical-axis position;
- nominal emitting-plane position along that axis;
- maximum source/aperture envelope;
- local source geometry metadata;
- any local mixing optic that is part of the LEM.

The head optics can then be designed against an allowed optical envelope instead
of one exact COB.

For example:

- a Vesta LEM may have locally mixed WW/CW COBs and need little local mixing;
- a split-white Thrive LEM may include a short mixing chamber or diffuser before
  presenting its standardized output aperture.

This allows vendor-specific mixing problems to remain inside the replaceable
module where possible.

## 7. Base high-power electrical interface

Phase 1 requires a two-white-channel base profile, provisionally named
**LEM-TW2**.

LEM-TW2 exposes two independently controllable LED loads:

- Channel 0 — warm-white role;
- Channel 1 — cool-white role.

Each channel is logically a two-terminal constant-current load.

The external interface shall not require the controller to know how many COBs,
series strings, parallel branches, or current-sharing elements exist inside the
LEM.

The eventual LEM-TW2 electrical profile must define:

- allowable forward-voltage range per channel;
- continuous current range per channel;
- maximum module continuous power;
- transient limits;
- polarity;
- connector current/voltage rating;
- insulation/clearance requirements;
- whether channel returns are isolated or may be common.

Those values remain OPEN until the prototype bank is dimensioned.

The first PSM should be designed around a **declared compatibility envelope**,
not around one Bridgelux part number.

## 8. Low-voltage module interface

The LEM needs a low-power interface independent of LED drive power.

Logically it should provide:

- module presence/identity;
- primary temperature sensing;
- module descriptor/calibration access;
- optional additional temperature/sensor data.

The high-current and low-voltage interfaces are logically separate even if a
future physical connector combines them.

### 8.1 Mandatory primary temperature path

The primary thermal-protection signal should be a simple local sensor physically
coupled to the light engine, preferably passive/analog for fail-safe behavior.

Its exact NTC/PTC characteristic is not yet frozen.

The final interface shall define open-circuit and short-circuit behavior as
faults.

Optional additional digital sensors may exist, but they shall not be the only
thermal-protection mechanism.

### 8.2 Module descriptor and calibration memory

A replaceable light engine should carry its own identity and calibration rather
than require every controller to be manually reconfigured after replacement.

The preferred Phase 1 direction is a small nonvolatile memory on the LEM,
accessible through a low-voltage serial interface.

The exact memory part and physical bus are not frozen, but the logical descriptor
should eventually contain at least:

```text
interface_major
interface_minor
module_family
module_revision
module_serial_or_uuid

channel_count
channel_role[]
recommended_current_range[]
maximum_continuous_current[]
expected_forward_voltage_range[]

maximum_continuous_module_power
temperature_sensor_definition
maximum_operating_temperature

calibration_format_version
calibration_data_reference_or_block
manufacturing_test_revision
```

The descriptor makes the module self-describing for configuration and
calibration.

It is **not** the sole safety authority.

## 9. Safety hierarchy

A corrupted module descriptor must not be able to command unsafe LED current.

Safety limits are layered:

```text
hardware power-stage absolute limits
              ↓
power-stage compatibility envelope
              ↓
LEM declared limits
              ↓
controller operating/calibration policy
              ↓
user request
```

The effective command is constrained by the most restrictive applicable limit.

Required behavior:

- unknown/incompatible interface major version -> LED drive inhibited;
- missing primary temperature sensor -> LED drive inhibited or restricted to a
  deliberately defined diagnostic mode;
- overtemperature -> hardware/low-level shutdown path independent of normal UI;
- module replacement while LED power is enabled -> unsupported in Phase 1.

The LEM is **service-replaceable, not hot-swappable**.

Avoiding hot-swap in Phase 1 greatly simplifies high-power connector safety and
inrush/contact design.

## 10. Controller-to-power-stage contract

The Controller Module should not directly generate high-power LED current.

The stable CM-to-PSM logical contract should expose behaviors such as:

- enable/disable output;
- request per-channel current;
- read measured current;
- read channel voltage where available;
- read power-stage temperature;
- receive fault state;
- receive power-stage capability envelope.

The exact physical communication protocol remains OPEN.

This separation allows a future 2-channel 300 W PSM and a future higher-power or
multi-channel PSM to implement the same controller-facing behavior where
possible.

## 11. Calibration ownership

Calibration has two layers:

### Module calibration

Travels with the LEM and describes emitter-specific behavior, for example:

- channel output versus current;
- CCT/Duv mixing coefficients or lookup data;
- thermal correction terms;
- module-specific characterization identity.

### Product calibration / policy

Lives in the controller and describes product-level behavior, for example:

- requested CCT/intensity interpretation;
- user-visible limits;
- thermal derating policy;
- preferred mixing strategy;
- premium feature behavior.

Changing the LEM should therefore require loading new module calibration, not
rewriting product firmware.

## 12. Optional future spectral channels

Phase 1 shall not pre-allocate a large fixed number of unused high-power channel
pins.

That would violate KISS/YAGNI.

Instead:

- LEM-TW2 defines the two-channel base light;
- a future multi-spectral engine may define a new LEM electrical profile;
- if the existing PSM cannot support it, the PSM is replaced together with the
  LEM;
- the Controller Module should remain reusable through the same conceptual
  current-channel/control abstraction.

This keeps Phase 1 simple without blocking a premium architecture later.

## 13. What must be frozen before Rev A hardware

Before the first real head PCB/mechanics are released, the following interface
facts must become exact hardware contracts:

1. LEM mechanical datum and bolt pattern.
2. Thermal contact plane dimensions and mounting force.
3. LEM-TW2 voltage/current/power envelope.
4. High-power connector family and pinout.
5. Low-voltage connector family and pinout.
6. Primary temperature-sensor characteristic.
7. Module descriptor bus and minimum schema.
8. Optical-axis and emitting-plane datum.
9. Module-presence/fault behavior.
10. CM-to-PSM electrical/control connector and protocol.

These values should be derived from the 250–350 W prototype and then captured in
an ADR/interface specification.

## 14. Immediate next step

Dimension the split-white Thrive prototype sufficiently to determine:

- COB count;
- series/parallel arrangement;
- per-channel voltage/current;
- maximum expected module power;
- physical source envelope;
- thermal contact-area requirement.

Those measurements/calculations provide the missing values needed to turn this
study into the first versioned LEM-TW2 interface specification.

## References

1. Zhaga Consortium, **Books** — interface specifications for LED modules,
   drivers, connectors, and related luminaire components.
   https://zhagastandard.org/books
2. Zhaga, **Book 22 — Electrical Power Interface**.
   https://zhagastandard.org/books/overview?catid=11&id=124%3Aledset-power-interface-22&view=article
3. Zhaga, **Book 23 — LEDset information interface**.
   https://zhagastandard.org/component/content/article/ledset-information-interface-23?catid=11
4. Zhaga, **Book 21 — Linear socketable LED modules for SELV applications**,
   used as a replaceability/interoperability design reference rather than as an
   OpenSpectralLight form factor.
   https://zhagastandard.org/books/overview?catid=11&id=123%3Alinear-led-module-with-socket-21&view=article
