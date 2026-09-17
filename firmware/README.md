# Firmware

Embedded code is organized by product responsibility. The first firmware product
is `controller/`, which will own the lamp's portable control behavior and the
selected board integration.

The repository does not assume an RTOS, BLE stack, MCU family, or vendor SDK until
those choices are justified. Shared runtime code is not created until at least two
real firmware consumers need it.
