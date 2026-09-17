# Controller Firmware

The controller is the embedded authority for requested light output, channel
setpoints, power/thermal limits, sensor coordination, local controls, and
diagnostics.

As real code arrives, responsibilities should be separated only where useful:

```text
App/         portable product behavior and algorithms
Interfaces/  genuine hardware/service seams needed for isolation or tests
Drivers/     external-device protocols and vendor-event normalization
BSP/         MCU/board-specific startup and peripheral integration
Tests/       deterministic host tests for portable behavior
docs/        controller-specific implementation/bring-up documentation
```

Do not create all of these directories merely for symmetry. The selected MCU and
BLE implementation remain open decisions. Application logic must remain portable
enough to build and test on the host.
