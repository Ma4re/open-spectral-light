# Camera Interaction

## Purpose

This chapter explains why a temporally modulated light can look steady to a
person yet produce banding, brightness variation, or color variation in a
camera. It establishes the camera-side requirements that later constrain the
LED driver.

## 1. Exposure integrates light over time

A camera pixel does not measure instantaneous light. It integrates incident
light during its exposure interval.

For one pixel or row with exposure starting at `t_0` and exposure time
`t_e`, the captured signal is approximately proportional to

```math
Q(t_0) \propto \int_{t_0}^{t_0+t_e} L(t)\,dt.
```

Therefore exposure time acts as a temporal averaging window.

For a sinusoidally modulated source,

```math
L(t)=L_0\left[1+m\cos(2\pi f t+\phi)\right],
```

the residual modulation after exposure integration is scaled by

```math
\left|\frac{\sin(\pi f t_e)}{\pi f t_e}\right|.
```

Longer exposure relative to the modulation period tends to average the
modulation more strongly. Short exposures can reveal modulation that was almost
invisible at normal shutter times.

## 2. Rolling shutter

Many CMOS cameras and smartphones expose/read the image progressively by rows.
Different rows therefore begin their exposure at slightly different times.

For row `r`,

```math
t_r = t_0 + r\,\Delta t_{\mathrm{row}}.
```

If the light changes during the frame readout, each row can integrate a
different phase of the optical waveform.

The result can be:

- horizontal brightness bands;
- color bands when spectral channels are modulated differently;
- gradients from top to bottom;
- frame-to-frame brightness differences.

Research using rolling-shutter CMOS sensors intentionally exploits this
phenomenon to decode modulated LEDs, demonstrating that modulation much faster
than the video frame rate can still become spatial structure in an image.

## 3. Frame rate is not enough

A statement such as "works at 60 fps" is incomplete.

Camera interaction depends on at least:

- frame rate;
- exposure/shutter time;
- sensor row-readout time;
- rolling versus global shutter;
- phase relationship between exposure and light waveform;
- modulation frequency;
- modulation depth/duty cycle;
- whether different spectral channels share the same temporal waveform.

Sony's own camera guidance notes that LED lighting can cause banding at
hundreds or thousands of hertz and provides variable-shutter functions to align
camera exposure with a light's modulation cycle.

This is why OpenSpectralLight validates a **camera/shutter matrix**, not a single
frame-rate number.

## 4. Global shutter

A global-shutter sensor exposes all pixels over the same time interval.

This largely removes row-to-row spatial banding caused by rolling readout, but it
does not make temporal modulation irrelevant. If successive frames sample
different phases of the light waveform, frame brightness can still vary.

Global shutter therefore reduces one failure mode but does not eliminate the
need for low-TLM illumination.

## 5. PWM and exposure time

For a rectangular PWM waveform, the camera receives the correct average only
when the exposure window samples a representative portion of the PWM cycle.

If exposure time becomes comparable with, or shorter than, the PWM period,
captured brightness can become strongly phase-dependent.

With rolling shutter, that phase dependence varies across rows.

Raising PWM frequency generally helps, but no universal "safe frequency" exists
independent of exposure time, duty cycle, row timing, and modulation depth.

## 6. Tunable-white color banding

The camera problem is more severe if warm and cool channels have different
temporal waveforms.

Suppose a requested intermediate white is produced by alternating WW and CW
rather than emitting both concurrently. The time-averaged SPD may be correct:

```math
\bar{S}(\lambda)=
D_{WW}S_{WW}(\lambda)+D_{CW}S_{CW}(\lambda).
```

A rolling-shutter row, however, may integrate a different WW/CW ratio from the
next row. This can create chromatic banding even when total illuminance is
approximately constant.

For camera-oriented tunable white, concurrent spectral mixing is therefore
preferable to time-division color mixing.

## 7. Camera spectral response

A camera does not implement the CIE standard observer directly.

The recorded RGB values depend on:

- sensor spectral sensitivities;
- color-filter array;
- lens transmission;
- IR/UV filtering;
- analog gain/ISO implementation;
- white balance;
- color matrices and image processing;
- RAW development or video profile.

Two spectra that are metamers for a human standard observer can therefore render
differently on different cameras.

This is why CCT, Duv, and even excellent human color-rendering metrics cannot by
themselves guarantee identical camera color.

## 8. Validation matrix for Phase 1

The creative requirement remains normal-speed acquisition through 60 fps.

The initial **acceptance-oriented** camera matrix should cover:

- 24 fps near 1/48 s;
- 25 fps near 1/50 s;
- 30 fps near 1/60 s;
- 50 fps near 1/100 s;
- 60 fps near 1/120 s.

These are the agreed 180-degree-equivalent exposure cases.

A separate **robustness characterization** should also exercise faster exposure
times such as approximately:

- 1/250 s;
- 1/500 s;
- 1/1000 s.

Those faster shutter points are characterization targets, not yet guaranteed
product specifications. Their purpose is to reveal the temporal limit of the
driver rather than silently assume the light is safe at every shutter speed.

Testing should include at least:

- a representative interchangeable-lens rolling-shutter camera;
- representative smartphones;
- multiple dimming levels;
- 2700 K, an intermediate CCT, and 6500 K;
- both static scenes and motion where practical.

## 9. Measurement versus camera testing

Real-camera tests are necessary but insufficient by themselves because a
particular camera may hide a waveform that another camera exposes.

The final validation method therefore needs both:

1. direct optical waveform measurement with a fast detector/oscilloscope; and
2. real-camera image/video testing.

The waveform explains the source. The cameras test the actual use case.

## Design implications

For OpenSpectralLight:

- frame rate alone is not a flicker specification;
- the driver must be evaluated against shutter time and rolling-shutter timing;
- 24/25/30/50/60 fps at 180-degree-equivalent exposure is the Phase 1 creative
  acceptance baseline;
- 1/250, 1/500, and 1/1000 s are useful robustness probes before freezing a
  temporal specification;
- tunable-white channels should emit concurrently in normal operation rather
  than alternate to synthesize CCT;
- any shared PWM used for brightness should preserve the WW/CW spectral ratio
  throughout the pulse;
- global-shutter compatibility does not excuse a highly modulated source;
- color validation must include real cameras because camera spectral response is
  not the CIE standard observer.

## References

1. Sony, **How to reduce camera flickering (horizontal banding / discoloration)**.
   https://www.sony.com/electronics/support/e-mount-body-ilce-3000-series/articles/00122281
2. Sony, **Part or all of the image may change color, showing banding or
   flickering**.
   https://www.sony.com/electronics/support/memory-camcorders-fdr-ax-series/fdr-ax100/articles/00008839
3. N. T. Le et al., **Performance Analysis of Visible Light Communication Using
   CMOS Sensors**, Sensors 16(3), 309, 2016.
   https://www.mdpi.com/1424-8220/16/3/309
4. IEEE, **IEEE 2020-2024 — IEEE Standard for Automotive System Image Quality**,
   published 2025-03-21. The standard includes camera flicker among the image-
   quality attributes addressed.
   https://standards.ieee.org/ieee/2020/6765/
5. Texas Instruments, **TPS63802HDKEVM User's Guide**, high-side LED driver
   example using current dimming for camera illumination.
   https://www.ti.com/lit/pdf/slvubu0
