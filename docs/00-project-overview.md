# Project Overview

## Purpose

OpenSpectralLight is a modular lighting platform intended to serve two related
use cases:

1. A practical, high-quality tunable light for professional-looking photography
   and video in home/studio environments.
2. An educational and experimental embedded platform for understanding LED
   current control, spectral mixing, color science, thermal behavior, flicker,
   calibration, and closed-loop measurement.

The project should remain useful at several cost levels. Premium sensing and
additional spectral channels must improve capability without becoming mandatory
for a basic build.

## Product principles

- Standalone local operation is primary; BLE may add local wireless control and telemetry.
- Hardware modules have stable responsibilities and revision independently when practical.
- MCU, driver, sensor, and emitter choices remain implementation details until an ADR freezes them.
- Optical and thermal performance are measured, not inferred from marketing values alone.
- The project avoids speculative abstraction and feature accumulation.

## Capability tiers

The tiers describe capability, not separate products.

### Basic

- Tunable-white light engine.
- Manual/local controls.
- Safe current and thermal limits.

### Professional

- Optional local BLE control.
- Extended dimming/temporal characterization beyond Phase 1's required camera
  acceptance baseline, when justified by real use cases.
- Enhanced thermal telemetry and compensation.
- Optional additional emitter channels for spectral correction.

The **Basic** capability is not allowed to postpone the agreed normal-video
flicker/banding requirements to a premium tier.

### Measurement / lab

- Multispectral or spectrometer daughterboard.
- Characterization of CCT, Duv, spectral distribution, and flicker.
- Calibration tooling and advanced color-quality metrics when measurement quality supports them.
