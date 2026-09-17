# Radiometry and Photometry

## Purpose

OpenSpectralLight controls and measures light, but "amount of light" is not a
single physical quantity. This chapter establishes the radiometric and
photometric vocabulary used by the project before any emitter, sensor, optical
system, or calibration method is selected.

The project uses CIE terminology where practical. Definitions in this document
are explanatory summaries; the CIE International Lighting Vocabulary remains
the normative terminology reference.

## 1. Radiometry and photometry are different measurement systems

**Radiometry** measures quantities associated with optical radiation. It is
based on physical radiant energy and power and does not weight the result by
human visual sensitivity.

**Photometry** measures optical radiation after weighting it by a specified
spectral luminous-efficiency function, normally the photopic function
`V(lambda)` for light-adapted human vision.

This distinction is fundamental for a spectrally tunable source. Two spectra
can contain the same radiant power and produce different photometric values, or
produce similar photometric values while distributing their radiant power very
differently across wavelength.

## 2. Core radiometric quantities

| Quantity | Symbol | Meaning | SI unit |
|---|---:|---|---:|
| Radiant energy | `Q_e` | Energy carried by optical radiation | J |
| Radiant flux / radiant power | `Phi_e` | Radiant energy transferred per unit time | W |
| Radiant intensity | `I_e` | Radiant flux per unit solid angle from a source | W/sr |
| Irradiance | `E_e` | Radiant flux incident on a surface per unit area | W/m^2 |
| Radiant exitance | `M_e` | Radiant flux leaving a surface per unit area | W/m^2 |
| Radiance | `L_e` | Directional radiant flux per projected area and solid angle | W/(m^2 sr) |

Radiant flux answers **how much optical power exists in total**. Irradiance
answers **how much optical power reaches a surface per unit area**. Radiant
intensity adds direction. Radiance additionally accounts for the projected
source/receiver area and is therefore especially important when discussing
source brightness and optical systems.

These quantities are related but are not interchangeable.

For example, a one-watt optical source does not imply a particular irradiance on
a subject. Irradiance also depends on the source geometry, angular distribution,
distance, optics, and illuminated area.

## 3. Photometric counterparts

Photometric quantities have analogous geometric meanings, but their spectral
content is weighted according to visual sensitivity.

| Radiometric quantity | Photometric counterpart | Symbol | SI unit |
|---|---|---:|---:|
| Radiant flux | Luminous flux | `Phi_v` | lm |
| Radiant intensity | Luminous intensity | `I_v` | cd |
| Irradiance | Illuminance | `E_v` | lx = lm/m^2 |
| Radiant exitance | Luminous exitance | `M_v` | lm/m^2 |
| Radiance | Luminance | `L_v` | cd/m^2 |

A lux measurement is therefore not a direct measurement of optical watts. It is
a photometric measurement whose value depends on the spectrum of the incident
light and the defined visual weighting.

## 4. Spectral quantities

A radiometric quantity can be expressed as a function of wavelength. For a
generic quantity `X`, its spectral distribution with respect to wavelength is
written conceptually as

```math
X_\lambda(\lambda) = \frac{dX}{d\lambda}
```

and the total quantity over a wavelength interval is

```math
X = \int X_\lambda(\lambda)\,d\lambda.
```

The exact unit of `X_lambda` depends on both `X` and the wavelength unit used.
For example, spectral irradiance may be reported in `W/(m^2 nm)`.

The project treats the wavelength basis and units as part of the data contract.
A table of numbers without wavelength coordinates, interval information, and
units is not a complete spectral measurement.

See [`spectral-power-distribution.md`](spectral-power-distribution.md) for the
project's detailed treatment of spectral distributions.

## 5. From radiometry to photometry

For photopic vision, a spectral radiometric quantity is weighted by the CIE
photopic spectral luminous-efficiency function `V(lambda)`.

For spectral irradiance, illuminance can be represented as

```math
E_v = K_{\mathrm{cd}} \int E_{e,\lambda}(\lambda)\,V(\lambda)\,d\lambda,
```

where `K_cd = 683 lm/W` is the SI luminous-efficacy constant and `V(lambda)` is
the standardized photopic weighting function.

The same principle applies to other corresponding radiometric/photometric
pairs.

The equation demonstrates why photometry alone does not preserve spectral
information: the spectrum is reduced to a weighted integral. Many different
spectra can therefore produce the same illuminance.

## 6. Solid angle and directional quantities

A solid angle `Omega` describes an angular extent in three dimensions and is
measured in steradians (`sr`). Directional quantities such as radiant intensity
and radiance depend on solid angle.

For a small source treated as a point source in the far field, irradiance on a
surface normal to the source direction follows an inverse-square relationship:

```math
E \propto \frac{1}{r^2}.
```

Doubling the distance then reduces irradiance to approximately one quarter.

This relationship is not a universal law for every practical light fixture. It
assumes point-source-like geometry and no change in the optical distribution.
Extended sources, near-field measurements, lenses, reflectors, diffusers, and
collimating optics can violate the assumptions required for the simple model.

## 7. Incidence angle and Lambert's cosine law

For a planar receiver illuminated by a collimated beam, the effective projected
area decreases as the angle from the surface normal increases. Under the usual
ideal assumptions,

```math
E(\theta) = E(0)\cos(\theta).
```

This is commonly called the cosine law of incidence. Practical detector heads
and optical systems have finite angular-response errors, so the ideal cosine
relationship should not be assumed to describe an instrument unless its
response is characterized.

The term **Lambertian source** describes a separate but related angular property:
an ideal Lambertian emitter has constant radiance with viewing direction, while
its radiant intensity varies with the cosine of the angle from the surface
normal.

## 8. What a single light meter can and cannot tell us

A conventional illuminance meter can be useful for repeatability, brightness
checks, and spatial-uniformity measurements when its spectral and angular
responses are adequate for the source being measured.

It cannot, from illuminance alone, determine:

- the spectral power distribution;
- the radiant power in individual wavelength bands;
- whether two equal-lux sources have the same chromaticity or color rendition;
- the spectral match between the source and a camera sensor;
- temporal modulation unless the instrument has appropriate temporal response.

Sensor capability therefore limits which claims the project can make. The
measurement-label policy in [`../05-color-science.md`](../05-color-science.md)
remains authoritative for whether a value is a setpoint, estimate, calibrated
estimate, or measurement.

## Design implications

For OpenSpectralLight:

- Radiometric, photometric, colorimetric, and camera-facing quantities must not be
  treated as synonyms.
- `lux` is useful for illuminance but is insufficient to characterize a
  spectrally tunable source.
- Spectral characterization is required whenever the wavelength distribution
  itself matters to a requirement or claim.
- Measurement geometry must be documented together with the measured value;
  distance, angle, illuminated area, and optical configuration can change the
  result.
- Inverse-square and cosine-law calculations may be used only when their
  assumptions are satisfied and stated.
- A future sensor or calibration method must be selected against the physical
  quantity it needs to support, not merely against a convenient numeric output.

## References

1. CIE, **CIE S 017/E:2020 — ILV: International Lighting Vocabulary, 2nd
   Edition**. https://doi.org/10.25039/S017.2020
2. CIE e-ILV, **Radiometry**, term 17-25-005.
   https://cie.co.at/eilvterm/17-25-005
3. CIE e-ILV, **Photometry**, term 17-25-013.
   https://cie.co.at/eilvterm/17-25-013
4. CIE e-ILV, **Radiant flux / radiant power**, term 17-21-038.
   https://cie.co.at/eilvterm/17-21-038
5. CIE e-ILV, **Radiance**, term 17-21-049.
   https://cie.co.at/eilvterm/17-21-049
6. Y. Ohno, **Radiometry and Photometry: Review for Vision Optics**, NIST,
   2000. https://www.nist.gov/publications/radiometry-and-photometry-review-vision-optics
7. Y. Ohno, **Photometry**, *Handbook of Visual Optics*, NIST, 2017.
   https://www.nist.gov/publications/photometry-chapter-handbook-visual-optics
