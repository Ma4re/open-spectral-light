# LED Emission Behavior

## Purpose

This chapter summarizes the emitter behavior that matters when OpenSpectralLight
turns electrical commands into a repeatable photographic light output. It
explains why an LED channel cannot be modeled only by nominal wattage, CCT, or a
single reference spectrum.

The chapter is intentionally implementation-neutral. Particular emitters,
drivers, thermal hardware, and channel counts are selected in engineering
studies and ADRs, not here.

## 1. White-light generation

LEDs are narrow-band semiconductor emitters. Practical white LEDs commonly use
a blue pump LED plus wavelength-converting phosphor. White light can also be
produced by mixing multiple colored emitters or by combining phosphor-converted
white channels with colored correction channels.

These approaches are not equivalent. They differ in spectrum, efficacy, color
rendering, thermal behavior, optical mixing, driver complexity, and controllable
chromaticity.

For a multi-channel source, the first-order spectral model is

```math
S_{\mathrm{out}}(\lambda) = \sum_i a_i S_i(\lambda),
```

where `S_i(lambda)` is the characterized spectrum of channel `i` and `a_i`
is an optical scaling coefficient. The coefficient is not automatically equal
to PWM duty cycle or normalized drive current.

## 2. Drive current and optical output

Increasing LED current generally increases optical output, but neither luminous
flux nor radiant flux should be assumed perfectly proportional to current over
the complete operating range.

At higher current density, LED efficacy normally decreases. This behavior,
commonly called efficiency droop, means that doubling electrical current does
not guarantee twice the useful optical output.

The current-to-light transfer function must therefore be characterized over the
actual operating range. A control algorithm may use a fitted or tabulated
transfer function rather than assuming

```math
a_i = \frac{I_i}{I_{i,\mathrm{rated}}}.
```

## 3. Junction temperature

The LED junction temperature is more important than ambient temperature alone.
A useful first-order thermal relation is

```math
T_j \approx T_c + P_{\mathrm{heat}} R_{\theta jc},
```

where `T_j` is junction temperature, `T_c` is a specified case/reference
temperature, `P_heat` is heat flowing through the package, and
`R_{theta jc}` is junction-to-case thermal resistance.

The exact thermal network depends on the package and mounting method, so the
manufacturer's specified reference point and thermal model must be used.

Higher junction temperature normally reduces luminous flux. It can also alter
the semiconductor emission and phosphor conversion, shifting chromaticity and
the spectral distribution. Thermal design is therefore part of optical
repeatability, not only a reliability requirement.

## 4. Current and temperature change the spectrum

A reference SPD measured at one current and temperature is not a universal
description of an LED channel.

Relevant mechanisms include:

- pump-die wavelength shift with junction temperature;
- temperature-dependent phosphor conversion efficiency;
- different thermal behavior of warm and cool phosphor systems;
- current-dependent spectral and chromaticity changes;
- long-term material aging and chromaticity shift.

For OpenSpectralLight, emitter characterization must record the operating
conditions associated with each measured spectrum. A mixing model that uses a
single reference SPD per channel must be validated against current and
temperature before it is trusted over the full operating envelope.

## 5. Binning and unit-to-unit variation

Nominal part number and nominal CCT do not uniquely define the output of every
physical LED. Manufacturers sort LEDs into flux and chromaticity bins, often
expressed using CCT/chromaticity ranges or SDCM/MacAdam-step tolerances.

A practical design must distinguish between:

- nominal product-family behavior;
- guaranteed bin limits;
- typical values;
- the actual measured behavior of one assembled light engine.

Calibration can compensate for some unit-to-unit differences, but it does not
remove the need to select parts with sufficiently tight and documented bins.

## 6. Tunable-white mixing

A common tunable-white light uses one warm-white phosphor channel and one
cool-white phosphor channel. Intermediate whites are created by changing the
relative channel contributions.

This is attractive because it needs only two controlled white channels, but two
endpoint chromaticities trace a mixing line in chromaticity space while the
Planckian locus is curved. A wide CCT span therefore cannot be assumed to stay
at constant or near-zero Duv without measurement and suitable endpoint
selection.

Consequently, a two-channel tunable-white source can provide excellent CCT
control yet still have limited independent tint/Duv authority.

Adding one or more auxiliary spectral channels can provide additional
chromaticity and spectral-shaping degrees of freedom, but increases driver,
calibration, optical-mixing, thermal, and control complexity.

## 7. Dimming method affects emitter state

Two broad dimming methods are relevant:

- **amplitude/current dimming**, which changes LED current;
- **PWM/time-domain dimming**, which changes the fraction of time the LED is
  energized.

Current dimming changes the electrical operating point continuously and can
therefore change efficacy and spectral behavior. PWM can preserve the on-state
current operating point but introduces temporal modulation. Hybrid approaches
are common.

The best choice cannot be made from LED behavior alone. Camera interaction and
temporal-light-modulation requirements are treated separately in
`temporal-light-modulation.md` and `camera-interaction.md`.

## 8. Aging and repeatability

LEDs usually degrade parametrically rather than failing suddenly. Luminous flux
and chromaticity can both change with operating time. Junction temperature,
drive current, package construction, phosphor temperature, optical flux density,
and material aging all influence long-term behavior.

A calibration performed at assembly therefore does not imply perfect lifetime
stability. The system architecture should allow recalibration where the intended
accuracy justifies it.

## 9. What an emitter datasheet must provide

Before an emitter becomes a serious candidate, the project should obtain enough
primary data to evaluate at least:

- current and forward-voltage operating range;
- maximum combined current for multi-channel packages;
- luminous/radiant output versus current;
- flux versus temperature;
- chromaticity or CCT behavior versus current and temperature;
- CRI/R9 and preferably TM-30 or spectral data;
- wavelength-resolved SPD where available;
- binning tolerances;
- junction-temperature limits and thermal resistance;
- LM-80 or equivalent maintenance information when available;
- mechanical LES dimensions and compatible optical/mounting data.

Marketing wattage alone is not a light-engine specification.

## Design implications

For OpenSpectralLight:

- electrical power, LED current, optical output, and camera exposure must remain
  separate quantities;
- control coefficients shall not assume perfectly linear current-to-light
  behavior without characterization;
- thermal sensing/estimation is relevant to optical stability, not only
  protection;
- endpoint SPDs must not be treated as temperature- and current-invariant unless
  measurements justify that approximation;
- a two-white-channel engine is the simplest credible tunable-white baseline,
  but wide-range CCT does not guarantee controlled Duv;
- additional spectral channels are justified only when they add measurable
  tint, spectral, or rendering capability;
- emitter selection must consider guaranteed bins and hot operating behavior,
  not only room-temperature typical values;
- the light engine should be designed so that later calibration can correct
  unit-to-unit and thermal variation where useful.

## References

1. U.S. Department of Energy, **LED Basics**.
   https://www.energy.gov/cmei/ssl/led-basics
2. U.S. Department of Energy, **Understanding LED Color-Tunable Products**.
   https://www.energy.gov/cmei/ssl/understanding-led-color-tunable-products
3. U.S. Department of Energy, **New Study Examines Lumen and Chromaticity
   Maintenance of LED Packages, Based on LM-80 Data**, 2020.
   https://www.energy.gov/cmei/ssl/articles/new-study-examines-lumen-and-chromaticity-maintenance-led-packages-based-lm-80
4. Nichia, **COB LED thermal-design and design application resources**.
   https://led-ld.nichia.co.jp/en/product/lighting_cob.html
5. Bridgelux, **Vesta Series Tunable White portfolio**.
   https://www.bridgelux.com/vesta-series
