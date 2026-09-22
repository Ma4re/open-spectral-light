# Phase 1 Temporal Driver Requirements Study

## 1. Purpose

This study converts the temporal-light-modulation and camera-interaction theory
into driver-level requirements without selecting a driver topology or part
number.

## 2. Primary objective

The Phase 1 driver should behave like a controllable current source first and a
"dimmer" second.

The desired hierarchy is:

1. generate the requested WW/CW average currents accurately;
2. preserve the requested spectral ratio over time;
3. minimize intentional optical modulation over the normal video range;
4. introduce PWM or hybrid behavior only where it provides a real low-level
   dimming benefit and has been camera-validated.

## 3. CCT control requirement

CCT shall not be synthesized by alternating full-power warm and cool pulses as
the default control method.

The preferred control model is

```math
I_{WW}=f_{WW}(CCT, Y)
```

```math
I_{CW}=f_{CW}(CCT, Y),
```

where the two channels may operate concurrently and `Y` represents the desired
output level.

The functions eventually come from calibration, not a linear Kelvin-to-current
assumption.

## 4. Dimming strategy

### Normal video range

**TARGET:** use continuous-current/amplitude control over as much of the
practical output range as the emitter and driver permit while maintaining:

- stable regulation;
- acceptable efficiency;
- acceptable spectral/chromaticity behavior;
- useful control resolution.

The minimum continuous-current level is OPEN until the selected matrix is
characterized.

### Deep dimming

If continuous-current control becomes unsuitable at low output, a hybrid region
may use PWM or another temporal technique.

If PWM is used:

- WW and CW should share the same temporal gate when both channels are active,
  unless testing proves another method equivalent;
- carrier frequency shall not be chosen from human flicker perception alone;
- modulation must be measured optically;
- the transition between continuous and PWM regions must not create visible
  brightness or color discontinuity.

No PWM frequency is frozen by this study.

## 5. Electrical channel behavior

The eventual driver architecture should support:

- independently commanded WW and CW average current;
- current measurement or a sufficiently accurate current-regulation model for
  each channel;
- hardware overcurrent limiting;
- thermal derating/protection independent of normal firmware control;
- synchronized temporal control if a PWM/hybrid mode exists;
- deterministic startup/shutdown without uncontrolled flashes;
- defined behavior during MCU reset or loss of control signal.

Exact sensing accuracy, switching frequency, topology, and bus voltage remain
OPEN.

## 6. Temporal validation points

Waveform characterization should be performed at least at:

- 100 %;
- 75 %;
- 50 %;
- 25 %;
- 10 %;
- the eventual minimum continuous-current point;
- representative deeper-dimming points if a hybrid region exists.

Repeat at:

- warm endpoint;
- intermediate mixed CCT;
- cool endpoint.

The optical waveform should be checked for:

- intentional PWM;
- current-loop ripple;
- switching ripple transferred into optical output;
- beat frequencies;
- channel timing mismatch;
- startup/shutdown transients.

## 7. Camera acceptance baseline

The Phase 1 acceptance baseline remains:

| Frame rate | Nominal exposure |
|---:|---:|
| 24 fps | ~1/48 s |
| 25 fps | ~1/50 s |
| 30 fps | ~1/60 s |
| 50 fps | ~1/100 s |
| 60 fps | ~1/120 s |

No visible brightness or color banding should occur in the validated camera set
under this matrix.

Characterization should also probe 1/250, 1/500, and 1/1000 s to identify the
temporal limit, but those points are not yet guaranteed product requirements.

## 8. Driver architecture consequences

These requirements favor a topology with true regulated current control rather
than a design whose only brightness mechanism is low-frequency PWM.

Prototype A power-stage work now prefers **independent buck regulation per COB
branch from a nominal 48 V bus** because the branch voltage remains below the
bus and independent regulation avoids parallel-LED current-sharing problems.

This remains a prototype architecture rather than a frozen driver implementation.
The baseline branch-controller candidate is TI LM3409HV because its 75 V input
range, high-side current sense, 250:1 analog dimming, and published 48 V -> 42 V
reference design align closely with the current envelope.

A synchronized fixed-frequency alternative such as TPS92691 remains relevant if
bench testing shows that unsynchronized multi-branch switching creates
unacceptable EMI or optical beat behavior.

The final implementation still depends on:

- measured branch voltage/current behavior;
- low-current spectral behavior;
- minimum useful analog-current point;
- branch efficiency and thermal loss;
- EMI/temporal measurements;
- final external-DC input and protection envelope.

## 9. Decision gate

Do not freeze the driver until:

- the emitter's safe simultaneous WW/CW envelope is confirmed;
- current-versus-SPD/chromaticity behavior is measured;
- the minimum useful analog-current point is known;
- camera tests show whether a hybrid/PWM region is required;
- temporal waveforms have been measured at representative dimming points.

The preferred outcome is the simplest current-control architecture that meets
the camera matrix without relying on camera-side anti-flicker features.

## References

1. CIE TN 012:2021, **Guidance on the Measurement of Temporal Light Modulation
   of Light Sources and Lighting Systems**.
   https://www.cie.co.at/publications/guidance-measurement-temporal-light-modulation-light-sources-and-lighting-systems
2. CIE 249:2022-Cor1, **Visual Aspects of Time-Modulated Lighting Systems**.
   https://www.cie.co.at/publications/visual-aspects-time-modulated-lighting-systems-incl-corrigendum-1
3. Nichia, **How to Control the Luminous Intensity of the LEDs**.
   https://led-ld.nichia.co.jp/api/data/spec/tech/SP-QR-C2-210734-1-E_How%20to%20Control%20the%20Luminous%20Intensity%20of%20the%20LEDs.pdf
4. Texas Instruments, **TPS63802HDKEVM User's Guide**.
   https://www.ti.com/lit/pdf/slvubu0
5. Sony, **How to reduce camera flickering**.
   https://www.sony.com/electronics/support/e-mount-body-ilce-3000-series/articles/00122281
