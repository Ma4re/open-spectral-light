# Power Hardware

Responsibility: convert the project input supply into controlled LED-channel
current, enforce electrical protection, and expose the status/telemetry required
by the controller.

Not yet frozen:

- Input voltage and power target.
- LED-driver topology/IC.
- Channel count for the first revision.
- Current-sense architecture.
- Fan/power-actuator placement.
- Protection thresholds and connector pinout.

The first design should optimize for a reliable tunable-white build before adding
spectral-correction channels.
