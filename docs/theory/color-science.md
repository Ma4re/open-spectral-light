# Color Science

## Purpose

This chapter explains how a spectral distribution is reduced to standard
colorimetric quantities and what information is lost in that process. It is the
theoretical counterpart to [`../05-color-science.md`](../05-color-science.md),
which defines the project's policy for user-visible metrics and measurement
claims.

Colorimetry is a standardized model of visual color matching. It is not a
complete model of appearance, preference, scene rendering, or camera response.
Those distinctions matter for a lighting platform intended for photography,
video, and spectral experimentation.

## 1. From a spectrum to tristimulus values

The CIE standard colorimetric systems represent a color stimulus with three
tristimulus values. For the CIE 1931 standard observer, a spectral distribution
`S(lambda)` can be reduced to `X`, `Y`, and `Z` using the color-matching
functions `x_bar(lambda)`, `y_bar(lambda)`, and `z_bar(lambda)`:

```math
X = k integral S(lambda) x_bar(lambda) dlambda
```

```math
Y = k integral S(lambda) y_bar(lambda) dlambda
```

```math
Z = k integral S(lambda) z_bar(lambda) dlambda.
```

The normalization factor `k` depends on the quantity and calculation context.
For chromaticity calculations from a relative source spectrum, any common
positive scale factor cancels when the tristimulus values are normalized.

For the CIE 1931 system, `y_bar(lambda)` is aligned with the photopic luminous-
efficiency function, linking the `Y` tristimulus value to photometric luminance
under the conditions defined by the standard system.

The important engineering fact is that the mapping from a sampled spectrum to
`X,Y,Z` is a weighted integration. The reverse mapping is not unique: three
tristimulus values cannot reconstruct an arbitrary spectrum.

## 2. Standard observers

The CIE defines standard colorimetric observers so calculations do not depend on
a particular individual's cone sensitivities.

Two systems commonly encountered are:

- **CIE 1931 standard colorimetric observer** — commonly called the `2 degree`
  observer and intended for relatively small centrally viewed fields;
- **CIE 1964 supplementary standard colorimetric observer** — commonly called
  the `10 degree` observer and intended for larger centrally viewed fields.

They use different color-matching functions and can produce different
tristimulus/chromaticity values for the same spectrum. A result is therefore not
fully specified unless the observer is known.

OpenSpectralLight must not silently mix values calculated with different
standard observers.

## 3. CIE xy chromaticity

The CIE `x,y,z` chromaticity coordinates are obtained by normalizing the
tristimulus values:

```math
x = X / (X + Y + Z)
```

```math
y = Y / (X + Y + Z)
```

```math
z = Z / (X + Y + Z) = 1 - x - y.
```

Only two coordinates are independent, so `x,y` are normally sufficient to
specify chromaticity in the CIE 1931 diagram.

Normalization removes overall magnitude. Two sources can therefore have the
same `x,y` chromaticity while having very different optical power or
illuminance.

More importantly, chromaticity also discards most spectral information. Many
different SPDs can map to the same `x,y` point.

## 4. CIE 1976 u'v' chromaticity

The CIE 1976 UCS chromaticity coordinates can be calculated from `X,Y,Z` as

```math
u' = 4X / (X + 15Y + 3Z)
```

```math
v' = 9Y / (X + 15Y + 3Z).
```

The 1976 UCS diagram was designed to provide more nearly uniform chromaticity
spacing than the CIE 1931 `x,y` diagram, although it is not a perfectly uniform
perceptual space.

The coordinate system used for a calculation must be stated. Distances in one
chromaticity diagram cannot generally be substituted numerically for distances
in another.

## 5. The Planckian locus

An ideal Planckian radiator changes chromaticity as its thermodynamic
temperature changes. The set of those chromaticities forms the **Planckian
locus**.

The locus provides a useful reference for describing approximately white light,
but most LED sources are not Planckian radiators. A phosphor-converted or
multi-channel LED source can have a chromaticity near the locus while having a
very different spectral distribution from a blackbody.

## 6. Correlated color temperature

**Correlated color temperature (CCT)** assigns a temperature to a non-Planckian
source by finding the chromaticity of the Planckian radiator that is nearest
under the defined method.

CCT is expressed in kelvin, but for an LED it is generally **not a physical
junction, phosphor, or enclosure temperature**. It is a colorimetric descriptor.

A single CCT value is insufficient to locate a white-light chromaticity because
chromaticity is two-dimensional. Sources on opposite sides of the Planckian
locus can have the same or nearly the same CCT while appearing differently
tinted.

## 7. Duv and distance from the Planckian locus

`Duv` provides the missing signed displacement relative to the Planckian locus.
In common lighting use, positive and negative values identify opposite sides of
the locus.

The exact numerical result depends on the standardized calculation method and
chromaticity geometry. Historical and current documents describe equivalent or
closely related forms using CIE 1960 `(u,v)` and the scaled CIE 1976
`(u', 2/3 v')` representation. For project calculations, the method must be
named rather than treating "Duv" as an implementation-independent formula.

ANSI/IES TM-40-24 provides a current IES method for computing CCT and distance
from the Planckian locus from chromaticity coordinates.

For practical source description, **CCT and Duv should be treated as a pair**.
Even together, however, they still do not identify a unique spectrum.

## 8. Metamerism

Two stimuli with different spectral distributions can produce the same
tristimulus values for a specified observer and viewing condition. They are
metamers under that condition.

This has several consequences for OpenSpectralLight:

- matching `x,y` does not guarantee matching SPD;
- matching CCT and Duv does not guarantee matching SPD;
- two sources that match for the CIE standard observer may not match for a
  camera sensor with different spectral responsivities;
- objects can render differently under two source spectra even when the sources
  have similar white-point chromaticities.

Metamerism is not an edge case for multi-channel spectral lighting; it is one of
the central reasons to preserve and reason about the spectrum itself.

## 9. Color rendition

White-point chromaticity describes the color of the source, not how object colors
will be rendered under that source.

Color-rendition methods compare the appearance or colorimetric behavior of
standardized samples under a test source and a reference condition.

### CRI

The CIE general color-rendering index `R_a` is a long-established average
fidelity metric. Special indices such as `R9` expose behavior that can be hidden
by the average, especially for saturated red content.

CRI remains common in specifications but does not completely characterize color
rendition.

### IES TM-30

ANSI/IES TM-30-24 uses a larger set of color evaluation samples and reports
multiple aspects of rendition. Its core outputs include:

- `R_f` — average color fidelity;
- `R_g` — average gamut area relative to the reference;
- hue-specific fidelity, chroma-shift, and hue-shift information;
- a color-vector graphic.

The value of TM-30 is not merely that it replaces one index with another; it
makes clear that color rendition is multidimensional and should not be reduced
to a single number when the application cares about spectral quality.

## 10. Human colorimetry and cameras

CIE standard observers model human color matching. A camera has its own spectral
sensitivities, color-filter array, processing pipeline, white-balance behavior,
and color transforms.

A source that is a good colorimetric match for the CIE observer can therefore
produce a different camera response from another nominally matching source.

This chapter does not attempt to model camera colorimetry. That belongs in the
future `camera-interaction.md` theory chapter. The current design consequence is
simply that human-observer metrics must not be treated as complete camera-
compatibility metrics.

## 11. What color coordinates do not contain

A chromaticity coordinate, CCT, or Duv value does not by itself contain:

- absolute light level;
- the complete SPD;
- temporal modulation;
- angular/spatial uniformity;
- color-rendering behavior for arbitrary surfaces;
- camera spectral response;
- measurement uncertainty.

Each metric should therefore be used for the question it actually answers.

## Design implications

For OpenSpectralLight:

- Spectral data are the physical starting point; XYZ, chromaticity, CCT, Duv, and
  rendition metrics are derived representations.
- Every colorimetric result must identify the observer and method when those
  choices can affect the value.
- `x,y`, `u',v'`, CCT, and Duv must not be used as interchangeable coordinate
  systems or distances.
- CCT should not be exposed as a complete description of white-light
  chromaticity; Duv or an equivalent two-dimensional chromaticity description is
  also required when the system claims chromaticity control.
- CCT and Duv do not replace an SPD when spectral matching, color rendition, or
  camera interaction matters.
- CRI, TM-30, or similar advanced metrics may only be reported when the
  measurement/calculation chain supports them, as required by
  [`../05-color-science.md`](../05-color-science.md).
- Future channel-mixing algorithms should operate from characterized channel
  spectra or validated models rather than assume that RGB-like coefficients
  uniquely represent physical output.

## References

1. CIE, **CIE 015:2018 — Colorimetry, 4th Edition**.
   https://doi.org/10.25039/TR.015.2018
2. CIE, **CIE S 017/E:2020 — ILV: International Lighting Vocabulary, 2nd
   Edition**. https://doi.org/10.25039/S017.2020
3. CIE e-ILV, **CIE 1931 standard colorimetric system**, term 17-23-045.
   https://cie.co.at/eilvterm/17-23-045
4. CIE e-ILV, **CIE 1964 standard colorimetric system**, term 17-23-046.
   https://cie.co.at/eilvterm/17-23-046
5. CIE e-ILV, **Chromaticity coordinates**, term 17-23-053.
   https://cie.co.at/eilvterm/17-23-053
6. CIE e-ILV, **CIE 1976 uniform-chromaticity-scale diagram**, term 17-23-073.
   https://cie.co.at/eilvterm/17-23-073
7. CIE e-ILV, **Planckian locus**, term 17-23-059.
   https://cie.co.at/eilvterm/17-23-059
8. CIE e-ILV, **Correlated colour temperature**, term 17-23-068.
   https://cie.co.at/eilvterm/17-23-068
9. Y. Ohno, **Practical Use and Calculation of CCT and Duv**, *LEUKOS*,
   2014. https://doi.org/10.1080/15502724.2014.839020
10. Illuminating Engineering Society, **ANSI/IES TM-40-24 — IES Method for
    Determining Correlated Color Temperature (CCT) and Distance from the
    Planckian Locus of Light Sources**, 2024.
    https://store.ies.org/product/technical-memorandum-ies-method-for-determining-correlated-color-temperature-cct-and-distance-from-the-planckian-locus-of-light/
11. Illuminating Engineering Society, **ANSI/IES TM-30-24 — IES Method for
    Evaluating Light Source Color Rendition**, 2024.
    https://store.ies.org/product/technical-memorandum-ies-method-for-evaluating-light-source-color-rendition/
