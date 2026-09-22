# System Architecture

## 1. Architectural view

```text
      Local controls / optional BLE
                    |
                    v
              Controller
          +---------+---------+
          |                   |
          v                   v
     Power stage          Sensor interface
          |                   |
          v                   v
      Light engine       Optional sensing
          |
          v
  Mixing optics / diffuser
          |
          v
         Light

 Mechanical/cooling surrounds the physical modules.
 Host software is optional and used for development, calibration, or convenience.
```

The controller is the product authority for requested output, power limits,
thermal behavior, and user control. The power stage regulates LED current. The
light engine owns emitters and their physical arrangement. Sensing measures
physical behavior but is not required for a basic lamp to emit controlled light.

## 2. Module boundaries

### Controller

Owns product behavior, local control, LED-channel setpoints, power budgeting,
thermal policy, sensor coordination, diagnostics, and the programming/debug
boundary. The MCU is not yet selected.

### Power stage

Owns high-power LED current regulation, electrical protection, channel actuation,
and power-stage telemetry exposed to the controller. The driver topology and ICs
are not yet selected.

The power stage is also treated as a replaceable module (PSM). A light engine
that remains inside one PSM compatibility envelope may be replaced without
changing the controller or power stage. A future light engine that requires a
substantially different voltage/current/channel envelope may replace the PSM and
LEM together while preserving the controller-facing product behavior.

### Light engine

Owns LEDs, emitter strings, MCPCB/layout, local heat spreader, module temperature
sensing, module identity/calibration data, channel identity, and optical source
geometry. Channel count and exact wavelengths are not yet frozen.

The light engine is treated as a **replaceable module (LEM)**. Vendor-specific
COB footprints, string arrangement, and local mixing remain inside the LEM. The
head sees a stable mechanical, thermal, optical, high-power electrical, and
low-voltage identification/sensing interface.

The Phase 1 base profile is a two-white-channel concept (`LEM-TW2`): warm and
cool loads are independently driven by the power stage. Exact voltage/current
limits, connectors, mounting dimensions, and descriptor bus are not yet frozen.

### Sensor module

Optional. Owns physical sensing such as spectral/color measurement, flicker
measurement, or dedicated temperature sensing when these functions justify a
separate board. A basic build works without it.

### Optics

Owns mixing, diffusion, beam shaping, reflectors, and optical characterization
fixtures. Optical revisions may evolve independently of electronics when their
interfaces remain compatible.

### Mechanical and cooling

Owns enclosure, heatsink interfaces, airflow, fan/duct geometry, mounting, and
physical protection.

### Host software

Optional. May provide calibration, characterization, logging, or convenience
control. It is not part of the lamp's minimum operational path.

## 3. Control and connectivity

- Local controls form the minimum operational control path.
- BLE is optional and carries local control and telemetry.
- BLE does not own electrical or thermal safety limits.
- The BLE implementation may be integrated into the controller MCU or provided by a separate module; this remains undecided.
- Firmware programming and debugging use the controller's wired development interface.

## 4. Dependency direction

Portable firmware policy and algorithms must not depend on MCU, board, RTOS, or
specific sensor/driver headers. Hardware implementations adapt upward through
small explicit boundaries when such boundaries are actually needed.

A nominal dependency direction is:

```text
App -> Interfaces <- Drivers/BSP
```

This is a responsibility rule, not a requirement to create an interface for every
class. Direct concrete dependencies are preferred when they do not violate
hardware isolation or testability.

## 5. Revision policy

Repository folder names describe responsibilities, not parts. Use
`hardware/controller/`, not `hardware/stm32h563/`; use `hardware/sensor/`, not a
specific sensor part number. Physical revisions are introduced only once real
schematics/layouts exist, for example `hardware/power/rev-a/`.


## 6. Replaceable module interfaces

OpenSpectralLight standardizes module boundaries rather than emitter brands.

The stable product hierarchy is:

```text
Controller Module
      |
      | control / telemetry
      v
Power Stage Module
      |
      | regulated LED channels
      v
Light Engine Module
      |
      | standardized optical datum
      v
Head optics / Bowens interface
```

The LEM contract is split into five logical interfaces:

1. mechanical datum and mounting;
2. rear thermal-interface plane;
3. optical axis / emitting-plane envelope;
4. regulated high-power LED channels;
5. low-voltage temperature, identity, and calibration access.

Exact physical connectors and dimensions are hardware contracts that will be
frozen only after the first 250–350 W prototype establishes realistic electrical,
thermal, and geometric envelopes.

The LEM carries emitter-specific calibration and identification. The controller
owns product-level policy. The power stage owns hard electrical protection.
A module descriptor may reduce allowed operating limits but may never increase
the hardware capability of the power stage.

Phase 1 modules are service-replaceable while unpowered; hot-swapping is not a
requirement.

The detailed pre-decision interface study is
[`studies/light-engine-module-interface.md`](studies/light-engine-module-interface.md).
