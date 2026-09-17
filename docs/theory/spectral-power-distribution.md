# Spectral Power Distribution

## Purpose

A spectrally tunable light cannot be described completely by brightness, CCT,
or an RGB triplet. This chapter defines how OpenSpectralLight treats spectral
distributions so that future emitter characterization, channel mixing,
calibration, and validation use consistent language.

The abbreviation **SPD** is common in lighting literature, but it is often used
loosely. In this project, a spectral data set must identify the underlying
physical quantity, wavelength basis, units, and whether the data are absolute or
normalized.

## 1. Spectral distribution

For a quantity `X`, the spectral distribution with respect to wavelength is
conceptually

```math
X_lambda(lambda) = dX / dlambda.
```

The total value over a wavelength interval is obtained by integration:

```math
X = integral X_lambda(lambda) dlambda.
```

The CIE International Lighting Vocabulary uses **spectral distribution** for the
density of a radiant, luminous, or photon quantity with respect to wavelength.
That means "SPD" does not by itself specify whether the data represent spectral
radiant flux, spectral irradiance, spectral radiance, or another quantity.

For project documentation, prefer explicit names such as:

- spectral radiant flux, `Phi_e,lambda`, for source optical output;
- spectral irradiance, `E_e,lambda`, for radiation incident on a surface;
- spectral radiance, `L_e,lambda`, when both area and direction matter.

## 2. Absolute and relative spectra

An **absolute spectrum** preserves calibrated magnitude and physical units. For
example, spectral irradiance may be reported in `W/(m^2 nm)`.

A **relative** or **normalized spectrum** preserves spectral shape but not the
absolute amount of optical power. A common normalization is

```math
S_norm(lambda) = S(lambda) / max(S(lambda)).
```

so the maximum value becomes one.

Normalization is useful for comparing shapes, locating peaks, or plotting
channels together. It destroys information about absolute output, so normalized
spectra must never be used as evidence of radiant power or illuminance without
an independent scale factor.

Every stored or published spectrum should therefore state whether it is
absolute, relative, or normalized and document the normalization rule.

## 3. Wavelength grid and numerical integration

Real instruments return samples rather than a continuous function. For samples
`S_i` measured at wavelengths `lambda_i`, an integral can be approximated by a
weighted sum. On a uniform wavelength grid with interval `Delta lambda`,

```math
integral S(lambda) dlambda approximately sum_i S_i Delta lambda.
```

When the wavelength spacing is not uniform, the actual interval between samples
must be included in the numerical integration.

This matters because the numerical values in a spectrum depend on whether the
reported density is per metre, per micrometre, or per nanometre. A spectral
value without its wavelength-density unit is ambiguous.

The following metadata should accompany project spectral data whenever it is
relevant:

- wavelength range;
- wavelength interval or sample coordinates;
- wavelength unit;
- spectral quantity and physical unit;
- absolute/relative/normalized status;
- normalization rule, if any;
- measurement geometry;
- instrument and calibration context;
- operating current, temperature, and other source conditions that materially
  affect the spectrum.

## 4. Spectral descriptors

Several scalar descriptors are useful, but none replaces the full spectrum.

### Peak wavelength

The **peak wavelength** is the wavelength at which the sampled or fitted spectral
quantity reaches its maximum.

```math
lambda_peak = arg max_lambda S(lambda).
```

For a multi-peaked or noisy spectrum, the reporting method should be stated.

### Spectral centroid

A power-weighted centroid can be defined as

```math
lambda_c =
    (integral lambda S(lambda) dlambda) /
    (integral S(lambda) dlambda).
```

The centroid describes the balance of a distribution but can lie at a wavelength
where the source emits little power.

### Full width at half maximum

For a single, reasonably well-behaved spectral peak, **FWHM** is the wavelength
separation between the two points where the spectral value reaches half of the
peak value.

FWHM is useful for characterizing spectral width, especially for narrowband
emitters. It is not a sufficient descriptor for asymmetric, multi-peaked, or
phosphor-converted spectra.

### Dominant wavelength

**Dominant wavelength is a colorimetric quantity**, not simply another name for
peak wavelength. It is obtained from chromaticity geometry relative to a chosen
achromatic reference and the spectral locus. A source can therefore have a
dominant wavelength that differs substantially from its spectral peak.

The project must not use `peak wavelength`, `centroid wavelength`, and
`dominant wavelength` interchangeably.

## 5. Additive spectral mixing

For independent optical channels whose outputs superpose linearly, the resulting
spectral quantity is the sum of the channel contributions:

```math
S_mix(lambda) = sum_i S_i(lambda).
```

If the spectral shape of channel `i` is stable and only its amplitude changes,
a simplified model is

```math
S_mix(lambda) = sum_i a_i S_i,ref(lambda),
```

where `a_i` is an optical scaling coefficient and `S_i,ref` is a characterized
reference spectrum.

The second model is an approximation, not a physical guarantee. An LED's
spectrum can change with current, junction temperature, aging, and unit-to-unit
variation. Therefore an electrical command such as PWM duty cycle or current
setpoint must not automatically be interpreted as a linear optical coefficient
without characterization.

Future LED-specific behavior belongs in `led-emission-behavior.md`; this chapter
only establishes the spectral-mixing model.

## 6. Spectral shape is not the same as chromaticity

Colorimetric systems reduce a spectrum to a small number of values. That is
useful, but information is lost in the reduction.

Different spectral distributions can produce the same tristimulus values for a
given standard observer. Such stimuli are **metamers** for that observer under
the specified conditions.

Consequently:

- identical CIE `x,y` coordinates do not imply identical spectra;
- identical CCT does not imply identical spectra;
- identical illuminance does not imply identical spectra;
- matching a human-observer chromaticity does not guarantee the same response
  from a camera sensor.

This is one of the central reasons OpenSpectralLight treats spectral data as a
first-class characterization artifact rather than reducing every result to CCT
or RGB values.

See [`color-science.md`](color-science.md) for the colorimetric reduction from a
spectrum to CIE tristimulus and chromaticity coordinates.

## 7. Spectral sampling is not spectral resolution

The wavelength interval between reported data points is not necessarily the
instrument's optical spectral resolution.

A spectrometer may report samples every `1 nm` while having a broader effective
instrument line-spread function. Conversely, resampling or interpolation can
create a finer numerical grid without adding physical information.

When a spectral feature is comparable to or narrower than the instrument
bandwidth, the measured shape and peak can be distorted. Spectral bandwidth,
wavelength accuracy, detector response, stray light, signal level, and
calibration can all affect a measurement.

Detailed metrology and uncertainty treatment belongs in the future
`measurement-and-calibration.md` chapter. Until then, project spectral data must
avoid implying a resolution or accuracy not supported by the instrument.

## 8. Spectral data representation

When spectral data become machine-readable project artifacts, the format should
preserve enough information to reconstruct the physical meaning of each sample.
At minimum, a representation should make the following unambiguous:

```text
wavelength
wavelength_unit
spectral_quantity
spectral_unit
value
normalization_state
```

Additional measurement metadata should be associated with the data set rather
than repeated per sample.

No file format or schema is frozen by this theoretical requirement.

## Design implications

For OpenSpectralLight:

- A channel must not be characterized only by a nominal LED wavelength or a
  marketing color name.
- Spectral measurements must state the physical spectral quantity, units,
  wavelength grid, and normalization state.
- Absolute and normalized spectra serve different purposes and must not be mixed
  in calculations without an explicit scale factor.
- Peak wavelength, centroid wavelength, dominant wavelength, and FWHM are
  different descriptors and must be labeled correctly.
- Multi-channel mixing may be modeled as spectral addition, but linear scaling
  of a reference spectrum requires experimental validation over the intended
  operating range.
- CCT, chromaticity, and illuminance are useful derived values but cannot replace
  the underlying spectrum when spectral behavior matters.
- Spectrometer sample spacing must not be reported as measurement resolution
  unless the instrument specification and method support that statement.

## References

1. CIE, **CIE S 017/E:2020 — ILV: International Lighting Vocabulary, 2nd
   Edition**. https://doi.org/10.25039/S017.2020
2. CIE e-ILV, **Spectral distribution / spectral concentration**, term
   17-21-029. https://cie.co.at/eilvterm/17-21-029
3. CIE, **CIE 015:2018 — Colorimetry, 4th Edition**.
   https://doi.org/10.25039/TR.015.2018
4. Y. Ohno, **Radiometry and Photometry: Review for Vision Optics**, NIST,
   2000. https://www.nist.gov/publications/radiometry-and-photometry-review-vision-optics
5. Y. Ohno, **Photometry**, *Handbook of Visual Optics*, NIST, 2017.
   https://www.nist.gov/publications/photometry-chapter-handbook-visual-optics
