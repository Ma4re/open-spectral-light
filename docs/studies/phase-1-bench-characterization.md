# Phase 1 Bench Characterization Plan

## 1. Purpose

This plan defines the first reproducible bench characterization of a high-power
tunable-white light engine before the LED matrix, driver topology, thermal
solution, DC input, or 1200 lx limit target is frozen.

The test answers four engineering questions:

1. How much useful light reaches the subject through realistic Bowens
   modifiers?
2. How do output, CCT, Duv, and spectrum change with current and temperature?
3. What electrical and thermal load must the final head handle?
4. Is a 300 W-class matrix sufficient, or is the larger 500 W-class matrix
   justified?

This is a characterization plan, not a compliance-certification procedure.

## 2. Measurement principles

The setup should follow the reproducibility principles of CIE S 025 and
ANSI/IES LM-79 where practical: document environmental conditions, electrical
conditions, geometry, stabilization, instrument identity/calibration, and
measurement uncertainty.

ANSI/IES TM-38 is particularly relevant because the source is tunable white.
ANSI/IES LM-82 is relevant when characterizing output/color as a function of
temperature.

A spectroradiometer is preferred for color characterization because one
measurement can provide the SPD and derived photometric/colorimetric quantities.
NIST notes that LED color measurements can be sensitive to wavelength scale,
bandpass, detector nonlinearity, and especially spectral stray light; instrument
specifications and calibration therefore matter.

## 3. Required bench capability

### Electrical

The bench shall provide two independently controllable constant-current channels
for WW and CW with:

- compliance voltage above the candidate matrix maximum forward voltage;
- current limit set independently for each channel;
- measured channel voltage and current;
- emergency output disable;
- no requirement for the final production driver topology.

For the 300 W-class candidate, endpoint rated-current characterization requires
up to 4.0 A per active channel. For the 500 W-class candidate it requires up to
8.4 A per active channel.

Mixed-channel high-power testing above conservative exploratory levels is
blocked until the manufacturer confirms the continuous simultaneous WW+CW
envelope.

### Thermal

Initial characterization shall use an intentionally oversized cooling solution
so emitter behavior can be separated from an undersized final enclosure.

Measure at minimum:

- ambient air temperature;
- matrix substrate/case temperature at the manufacturer-defined reference point;
- heat-spreader temperature close to the matrix;
- heatsink exhaust/outlet temperature if forced airflow is used.

Forced airflow is allowed and expected at these power levels. Record fan state
or fan speed with every stabilized operating point.

### Optical/color

Preferred instrumentation:

- calibrated illuminance meter with cosine-corrected receptor;
- calibrated spectroradiometer capable of exporting SPD;
- fixed optical bench/tape/laser reference for repeatable distance and
  alignment.

The spectroradiometer should report or allow calculation of:

- CIE x,y;
- CIE u',v';
- CCT;
- Duv;
- CRI Ra and R9;
- TM-30 Rf/Rg where supported;
- SPD in a documented wavelength grid.

Do not infer spectrometer optical resolution from sample spacing.

### Temporal

Temporal/flicker characterization is deferred until a representative dimming
driver exists. The later setup should use a fast photodiode/photometric detector
and oscilloscope in addition to real-camera tests.

## 4. Safety and fixture setup

High-power matrix testing involves high light intensity, hot surfaces, and
hundreds of watts of electrical power.

The test fixture shall include:

- mechanically clamped matrix and verified thermal interface before energizing;
- protective cover/guard against accidental contact with hot live parts;
- eye-safe working practice: do not view the energized bare matrix directly;
- current limiting active before output enable;
- accessible emergency shutdown;
- temperature monitoring active before high-power operation.

Until manufacturer guidance is received, the bench shall not intentionally
operate at absolute-maximum current/power ratings.

For prototype protection, stop a test if substrate/case temperature approaches
a manufacturer limit, temperature continues to rise without stabilization, or
any electrical/optical behavior becomes abnormal. A final operational
temperature limit will be selected only after manufacturer guidance and thermal
characterization.

## 5. Phase A — endpoint electrical/thermal characterization

Test WW and CW **separately first**.

For the 300 W-class candidate, characterize approximately:

```text
0.4 A
1.0 A
2.0 A
3.0 A
4.0 A
```

For the 500 W-class candidate:

```text
0.84 A
2.1 A
4.2 A
6.3 A
8.4 A
```

These correspond to approximately 10, 25, 50, 75, and 100 % of the published
endpoint test current.

At each point record:

- WW/CW current;
- forward voltage;
- electrical input power;
- ambient, substrate/case, and heatsink temperatures;
- central illuminance at the fixed bare-source reference geometry;
- SPD and derived color quantities;
- elapsed warm-up time.

The aim is to build measured functions such as

```math
\Phi_v = f(I, T_s)
```

and

```math
\mathrm{Duv} = g(I, T_s),
```

rather than assume linear optical output from drive current.

## 6. Stabilization procedure

For each important operating point:

1. start from a documented initial thermal condition;
2. energize the selected channels;
3. log electrical and thermal data continuously or at short regular intervals;
4. record optical/color data at startup and during warm-up;
5. declare the point stabilized only when the thermal and optical trends are
   sufficiently flat for repeatable comparison;
6. record the actual stabilization criterion used.

Initial characterization should sample at least around:

`0, 1, 2, 5, 10, 20, 30, 45, 60 min`

when the test lasts that long. The final stabilization criterion should be
derived from observed behavior rather than assuming that a fixed warm-up time
works at every power.

## 7. Phase B — tunable-white mixing trajectory

After Yujileds confirms the allowed simultaneous-current envelope, characterize
mixed operation.

Do **not** assume CCT is linear with WW/CW current ratio.

Choose enough points to resolve the full trajectory, including:

- 2700 K endpoint;
- approximately 3200 K;
- approximately 4000–4300 K;
- approximately 5000–5600 K;
- 6500 K endpoint.

For each target CCT, determine the actual WW/CW currents needed to reach it and
measure:

- total electrical power;
- SPD;
- CCT and Duv;
- x,y and u',v';
- CRI/R9/TM-30 where supported;
- illuminance;
- stabilized temperatures.

Repeat at multiple total-output levels so the project can determine whether the
mixing solution changes with dimming.

The most important outcome is the measured **CCT/Duv trajectory**, not merely
whether the firmware can display intermediate Kelvin values.

## 8. Phase C — modifier transmission and working-distance output

Use one documented physical sample of each reference modifier.

### Normal modifier

- approximately 90 cm Bowens octabox/deep softbox;
- double diffusion;
- no grid.

Measure on-axis illuminance at:

`1.0 m, 1.5 m, 2.0 m`

from the front diffusion plane.

### Limit modifier

- approximately 120 cm Bowens parabolic/octabox;
- double diffusion;
- test both without grid and with grid.

Measure on-axis illuminance at:

`1.0 m, 1.5 m, 2.0 m`

from the front diffusion plane.

At minimum run this at:

- 2700 K;
- approximately 4300 K;
- 6500 K;
- maximum intended continuous output.

The decisive result is the measured 2 m illuminance through the limit modifier.
It will determine whether the current 1200 lx target is practical.

## 9. Phase D — spatial uniformity

For the 90 cm and 120 cm modifiers, characterize a plane representative of a
portrait/half-body subject.

A practical first grid is a **1 m x 1 m plane sampled at 25 points**:

- 5 x 5 positions;
- 0.25 m spacing;
- plane perpendicular to the fixture axis.

At each point record illuminance. At selected center/edge/corner positions also
record chromaticity/CCT/Duv if the spectroradiometer measurement geometry allows
it.

Do not freeze an allowable uniformity percentage before seeing the measured
distribution and evaluating it photographically.

## 10. Phase E — thermal/acoustic characterization

Once a plausible cooling solution exists, repeat representative high-load
points through thermal equilibrium.

Record:

- electrical power;
- substrate/case temperature;
- heatsink temperatures;
- fan speed/control state;
- center illuminance;
- CCT/Duv drift;
- ambient temperature.

Acoustic testing should later record A-weighted sound pressure at a defined
distance and background noise floor. The intended 1–2 m shooting distance should
be represented.

The first goal is not a marketing dBA number; it is identifying whether the fan
becomes objectionable for music-video/interview recording and whether a larger
heatsink/lower-RPM fan strategy is needed.

## 11. Phase F — temporal/camera validation

This phase begins only after a representative production-like current-control
method exists.

Test:

- 100, 75, 50, 25, 10 % output;
- lower levels down to the minimum intended dimming point;
- 24/25/30/50/60 fps;
- 180-degree-equivalent shutter times;
- selected faster shutter times.

Use both an oscilloscope/fast optical detector and real cameras/smartphones.

Measure the optical waveform rather than claiming flicker-free behavior from PWM
frequency alone.

## 12. Data record

Each measurement record should include enough metadata to reproduce it:

```text
date/time
emitter part/revision/serial
WW current
CW current
WW voltage
CW voltage
electrical power
ambient temperature
substrate/case temperature
heatsink temperature
fan state/speed
modifier model
diffusion configuration
grid state
distance
measurement position
instrument model/serial/calibration status
SPD file/reference
illuminance
x
y
u_prime
v_prime
CCT
Duv
CRI_Ra
R9
TM30_Rf
TM30_Rg
notes
```

Raw measurements should be preserved. Derived summaries must not overwrite the
original observations.

## 13. Decision criteria after the first sample

The 300 W-class candidate remains preferred if it:

- comfortably supports the normal 90 cm key-light scenario;
- approaches the limit scenario closely enough that the extra size/noise/cost of
  the 500 W-class engine is not justified;
- maintains acceptable color behavior across CCT and thermal state.

Move to the 500 W-class candidate if the smaller matrix cannot satisfy the
intended real-world key-light use at 2 m with reasonable camera settings and
modifier losses.

If neither candidate can reach 1200 lx through the limit modifier without an
unacceptable thermal/acoustic system, revise the 1200 lx TARGET rather than
silently turning the project into an impractical fixture.

## References

1. CIE, **CIE S 025/E:2015 — Test Method for LED Lamps, LED Luminaires and LED
   Modules**.
   https://www.cie.co.at/publications/test-method-led-lamps-led-luminaires-and-led-modules
2. Illuminating Engineering Society, **ANSI/IES LM-79-24 — Approved Method:
   Optical and Electrical Measurements of Solid State Lighting Products**.
   https://store.ies.org/product/optical-and-electrical-measurements-of-solid-state-lighting-products/
3. Illuminating Engineering Society, **ANSI/IES LM-82-20 — Characterization of
   Optical and Electrical Properties of Solid-State Lighting Products as a
   Function of Temperature**.
   https://ies.org/standards/lighting-library/
4. Illuminating Engineering Society, **ANSI/IES TM-38-21(R26) — Photometric and
   Electrical Measurements of Tunable-White Solid-State Lighting Products**.
   https://ies.org/standards/lighting-library/
5. NIST, **Spectral measurement**.
   https://www.nist.gov/pml/sensor-science/optical-radiation/spectral-measurement
6. Yujileds, **LED Matrix Solution Introduction & Datasheet, V1.5**.
   https://www.yujiintl.com/wp-content/uploads/2022/09/Yujileds-LED-Matrix-Solution-V1.5.pdf
