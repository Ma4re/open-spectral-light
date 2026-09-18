# Temporal Light Modulation

## Purpose

This chapter explains time variation in emitted light and the distinction between
what a human observer sees and what a camera records. It provides the physical
basis for later driver, dimming, and validation decisions.

## 1. Temporal light modulation and temporal light artefacts

CIE TN 012:2021 defines **temporal light modulation (TLM)** as a measurable
change of light level or spectral distribution with time.

**Temporal light artefacts (TLAs)** are perceptual effects produced by TLM in a
human observer. CIE documents distinguish effects including flicker,
stroboscopic effect, and phantom-array effect.

That distinction is important for OpenSpectralLight:

- TLM is a property of the emitted waveform;
- TLA metrics describe human visual response under specified conditions;
- camera banding/flicker is a separate sampling interaction and cannot be
  declared safe only because a human-facing TLA metric is low.

## 2. Time-domain description

Let the measured optical quantity be `L(t)`. Useful basic quantities include

```math
L_{\mathrm{avg}} = \frac{1}{T}\int_0^T L(t)\,dt
```

and, for a periodic waveform, modulation depth

```math
m = \frac{L_{\max}-L_{\min}}{L_{\max}+L_{\min}}.
```

Modulation depth alone is not enough to characterize temporal behavior because
two waveforms with the same extrema can have very different frequencies,
harmonics, duty cycles, and perceptual/camera effects.

A complete characterization should retain the sampled waveform whenever
possible.

## 3. Frequency-domain description

Periodic TLM can be represented by a fundamental frequency plus harmonics.
Driver switching, control-loop ripple, PWM, rectification, beat frequencies, and
other electrical behavior can all appear in the optical spectrum.

A high carrier frequency does not automatically imply negligible modulation:
the modulation depth and harmonic content still matter, and a camera may sample
the waveform differently from the human visual system.

CIE TN 012:2021 emphasizes reproducible acquisition conditions including
sampling rate, measurement duration, detector characteristics, filtering, and
reporting of the measured waveform.

## 4. PWM dimming

Ideal PWM alternates the LED between an on-state current and zero:

```math
L_{\mathrm{avg}} \approx D\,L_{\mathrm{on}},
```

where `D` is duty cycle.

PWM has an important advantage: the LED can remain at a characterized on-state
current, reducing some of the spectral changes associated with very low
continuous current.

Its disadvantage is intentional TLM. A waveform that appears steady to a person
can still produce camera banding or frame-to-frame brightness variation.

Therefore a statement such as "PWM is above the flicker-fusion frequency" is not
a sufficient camera requirement.

## 5. Current/amplitude dimming

Amplitude dimming reduces the continuous LED current rather than turning the
channel fully on and off.

This can greatly reduce intentional TLM, which is attractive for imaging.
However, LED output is not perfectly linear with current and chromaticity may
change with current. Nichia explicitly documents that LED color can vary with
drive current and discusses PWM as one way to minimize that shift.

For OpenSpectralLight, current dimming therefore requires characterization of
both optical output and spectrum versus current.

## 6. Hybrid dimming

A practical driver may combine amplitude dimming over the main operating range
with PWM or another temporal method at very low output where continuous-current
control becomes inefficient, unstable, or spectrally undesirable.

Hybrid dimming is not automatically camera-safe. The transition point, waveform,
carrier frequency, modulation depth, and multi-channel timing must be validated.

## 7. Tunable-white temporal behavior

A tunable-white engine has an additional problem: **spectral modulation**.

If warm-white and cool-white channels are independently time-multiplexed, the
time-averaged output may have the desired CCT while the instantaneous spectrum
alternates between warmer and cooler states.

A human observer may integrate this into one apparent white, while a rolling-
shutter camera can sample different spectral mixtures at different rows.

For this reason, OpenSpectralLight should not use asynchronous or alternating
WW/CW PWM as the default mechanism for setting CCT.

If PWM is later required for overall intensity control, a preferred architecture
is:

- establish the WW/CW ratio through controlled channel currents;
- gate both active channels with a common synchronized temporal waveform for
  global brightness, when technically feasible;
- verify that instantaneous and time-averaged color remain acceptable.

Alternative schemes are allowed only after measurement demonstrates equivalent
camera behavior.

## 8. Human-facing metrics are not camera metrics

CIE 249:2022-Cor1 supersedes the original CIE 249:2022 and discusses human
visibility of temporal-light artefacts. The 2026 corrigendum specifically
clarifies that the provided SVM calculation method still needs further
verification.

Metrics such as PstLM and SVM may be useful for human-facing lighting quality,
but they do not replace camera testing.

OpenSpectralLight therefore separates:

- human-visible TLA characterization;
- optical waveform measurement;
- camera banding/flicker validation.

## 9. Measurement requirements

Temporal characterization should record at least:

- optical waveform versus time;
- average light level;
- modulation depth;
- dominant modulation frequencies and harmonics;
- dimming command;
- WW/CW operating point and requested CCT;
- electrical current/voltage waveform where useful;
- temperature and stabilization state;
- detector bandwidth, sample rate, and measurement duration.

The detector and acquisition chain must have enough bandwidth to measure the
waveform under test. An instrument that averages out the modulation cannot be
used to claim low TLM.

## Design implications

For OpenSpectralLight:

- "flicker-free" shall not be inferred from PWM frequency alone;
- the full optical waveform matters;
- camera validation is required independently of human TLA metrics;
- normal CCT control should avoid temporally alternating warm and cool spectra;
- independent average-current control of WW and CW is a desirable baseline;
- amplitude dimming is preferred for the principal video operating range when
  color stability and driver behavior support it;
- any PWM/hybrid region must be validated at representative cameras, shutter
  times, CCTs, and output levels;
- if both white channels are PWM-gated for intensity, synchronized gating is
  preferred so the intended spectral ratio is preserved during the pulse.

## References

1. CIE, **CIE TN 006:2016 — Visual Aspects of Time-Modulated Lighting Systems —
   Definitions and Measurement Models**.
   https://www.cie.co.at/publications/visual-aspects-time-modulated-lighting-systems-definitions-and-measurement-models
2. CIE, **CIE TN 012:2021 — Guidance on the Measurement of Temporal Light
   Modulation of Light Sources and Lighting Systems**.
   https://www.cie.co.at/publications/guidance-measurement-temporal-light-modulation-light-sources-and-lighting-systems
3. CIE, **CIE 249:2022-Cor1 — Visual Aspects of Time-Modulated Lighting Systems
   (incl. Corrigendum 1)**.
   https://www.cie.co.at/publications/visual-aspects-time-modulated-lighting-systems-incl-corrigendum-1
4. Nichia, **How to Control the Luminous Intensity of the LEDs**.
   https://led-ld.nichia.co.jp/api/data/spec/tech/SP-QR-C2-210734-1-E_How%20to%20Control%20the%20Luminous%20Intensity%20of%20the%20LEDs.pdf
5. Texas Instruments, **TPS63802HDKEVM User's Guide**, camera-light example using
   DC-current dimming to avoid rolling-shutter artefacts from PWM.
   https://www.ti.com/lit/pdf/slvubu0
