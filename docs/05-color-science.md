# Color Science Vocabulary and Measurement Policy

This document fixes terminology before firmware or UI begins exposing numbers.
It does not claim the project can measure every metric accurately with every
sensor option.

## Core terms

- **SPD (Spectral Power Distribution):** radiant power as a function of wavelength.
- **CIE XYZ:** tristimulus values computed from an SPD and standard observer color-matching functions.
- **CIE xy:** chromaticity coordinates derived from XYZ.
- **CIE 1976 u'v':** chromaticity coordinates useful for perceptual distance and Duv work.
- **CCT:** correlated color temperature of a chromaticity near the Planckian locus.
- **Duv:** signed distance from the Planckian locus; useful for green/magenta tint characterization.
- **CRI Ra:** legacy average color-rendering index. It is not a complete description of spectral quality.
- **R9:** CRI special index for strong red rendering; important to report separately when CRI is used.
- **TM-30 Rf/Rg:** fidelity and gamut metrics based on a broader color sample set.
- **SSI:** spectral similarity metric useful for comparing an illuminant with a reference spectrum.
- **Flicker/modulation:** temporal variation of optical output; must be measured with appropriate bandwidth.

## Measurement labels

Every user-visible or documented metric must be classified honestly:

- **Setpoint:** commanded value; not a measurement.
- **Estimated:** inferred from a limited sensor/model and not traceably calibrated.
- **Calibrated estimate:** corrected against a known reference over a documented range.
- **Measured:** directly derived from instrumentation with sufficient spectral/temporal capability for the stated metric.

Do not display extra decimal places that imply unsupported accuracy.

## Sensor policy

A low-cost multispectral sensor may be useful for closed-loop consistency,
relative comparison, and calibrated CCT/Duv estimation while still being
insufficient for defensible CRI/TM-30/SSI claims. Sensor capability and
calibration determine which metrics are exposed.

## Control policy

The project should distinguish:

1. requested output (for example CCT/brightness),
2. channel drive solution,
3. estimated or measured physical output.

The control implementation may evolve from two-channel tunable white to
multi-channel spectral optimization without changing these conceptual layers.
