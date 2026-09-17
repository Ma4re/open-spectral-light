# Testing and Validation

OpenSpectralLight separates deterministic software evidence from physical
validation. The test strategy grows with implemented capability; it does not add
rigs or frameworks before they are useful.

## Evidence layers

```text
System / photographic validation
HIL and characterization
Target integration / target build
Host component tests
Host unit tests
```

### Host unit tests

Use for portable algorithms and policies such as channel mixing math, limits,
power allocation, thermal-state logic, color conversions, parsers, and bounded
state machines. Tests are deterministic and test-first.

### Host component tests

Use fakes/simulated dependencies when several project components must collaborate.
Do not simulate electronics simply to obtain a green badge; keep the simulated
contract explicit.

### Target build/integration

Proves the selected MCU/toolchain can compile/link the production composition and
exercises peripherals where target hardware is required.

### HIL and characterization

Required for claims about:

- LED current accuracy and protection.
- Actual CCT/Duv/SPD.
- Flicker/modulation versus dimming level and camera mode.
- Thermal steady state, derating, shutdown, and recovery.
- Fan/acoustic behavior.
- BLE RF/interoperability behavior.
- Optical mixing/uniformity and beam behavior.

A host test cannot substitute for any of these.

## CI baseline

At repository foundation, CI only proves that the documented host CMake presets
configure, build, and execute the current CTest set. Formatting/static-analysis,
target builds, coverage, and HIL gates are added when real source and hardware
exist and the gates can be reproducible.

## No arbitrary coverage target yet

Coverage is useful for finding unexercised portable code, but no numeric threshold
is frozen before the first real `App` slice exists. When introduced, a threshold
must reflect meaningful deterministic behavior rather than encourage low-value
tests.
