# System Architecture

## 1. Architectural view

```text
 Local controls / optional BLE / wired service
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
physical behavior but must not be required for a basic lamp to emit controlled
light.

## 2. Module boundaries

### Controller

Owns product behavior, user/local control, LED-channel setpoints, power budgeting,
thermal policy, sensor coordination, diagnostics, and the wired programming/debug
boundary. The MCU is not yet selected.

### Power stage

Owns high-power LED current regulation, electrical protection, channel actuation,
and power-stage telemetry exposed to the controller. The driver topology and ICs
are not yet selected.

### Light engine

Owns LEDs, emitter strings, MCPCB/layout, thermal interface, channel identity, and
optical source geometry. Channel count and exact wavelengths are not yet frozen.

### Sensor module

Optional. Owns physical sensing such as spectral/color measurement, flicker
measurement, or dedicated temperature sensing when these functions justify a
separate board. A basic build must work without it.

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

## 3. Connectivity policy

- The lamp is local-first and offline-capable.
- BLE is the only planned wireless transport.
- BLE transports local control/telemetry; it does not become an authority for
  electrical or thermal safety limits.
- The exact BLE implementation may be integrated into the main MCU or provided by
  another module; this remains undecided.
- Wi-Fi, cloud backends, and Internet-dependent flows are out of scope.
- Firmware updates are wired. OTA/FOTA must not be introduced without an explicit
  architecture change.

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
