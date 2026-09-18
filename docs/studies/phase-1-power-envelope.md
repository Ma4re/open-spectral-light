# Phase 1 Light-Engine Power Envelope

## 1. Purpose

This study turns the Phase 1 optical target into a practical electrical and
thermal envelope before a specific emitter, driver, DC bus, connector, or
heatsink is frozen.

The current upper engineering target remains **1200 lx at 2 m** through the
large reference modifier: approximately 120 cm, double diffusion, and grid.

All values in this document are engineering estimates or manufacturer data. They
are not final product specifications.

## 2. Commercial fixture cross-check

Aputure publishes photometrics for the 120 cm Light OctaDome with its 2.5-stop
front diffusion:

| Fixture | Maximum system/output class | 1 m | 3 m |
|---|---:|---:|---:|
| LS 300x | <=350 W system input | 2335 lx | 347 lx |
| LS 600x Pro, 5600 K | <=600 W output / <=720 W input | 5230 lx | 825 lx |

The published measurements do not include a 2 m point and do not state that the
fabric grid was fitted.

For an order-of-magnitude check only, fitting the 1 m and 3 m points to a simple
extended-source form,

```math
E(z) = \frac{k}{(z+a)^2},
```

gives approximately:

| Fixture | Estimated 2 m illuminance, no grid |
|---|---:|
| LS 300x | ~723 lx |
| LS 600x Pro | ~1691 lx |

This interpolation is **not a product prediction**. It only shows that 1200 lx
at 2 m after a large, highly diffusive modifier sits between commercial
300 W-class and 600 W-class fixtures before the additional loss of a grid is
considered.

Scaling those fixture-level measurements by system input would place the
no-grid 1200 lx target in roughly the **510–580 W system-input region**. Because
the grid transmission is not published, the real limit case could require more.

The practical conclusion is that the 1200 lx limit case must be treated as a
**several-hundred-watt system problem** until bench measurements show otherwise.

## 3. Bridgelux Thrive cluster reference

Bridgelux Vesta Thrive 18 mm tunable-white
`BXRV-TR-2765S-40A0-A-23` is useful as a high-fidelity reference.

At nominal current the manufacturer reports approximately:

- warm endpoint: 31.7 W and 2730 lm stabilized DC at Tc = 85 C;
- cool endpoint: 32.2 W and 3104 lm stabilized DC at Tc = 85 C;
- typical CRI 98 / Thrive;
- 19.6 mm LES;
- maximum **combined** warm+cool current of 1.4 A and maximum total package
  power of 49 W.

At an endpoint, a nominal-current cluster therefore scales approximately as:

| COB count | Endpoint LED power | Warm flux @ 85 C | Cool flux @ 85 C |
|---:|---:|---:|---:|
| 6 | 190–193 W | 16.4 klm | 18.6 klm |
| 9 | 285–290 W | 24.6 klm | 27.9 klm |
| 12 | 380–386 W | 32.8 klm | 37.2 klm |
| 15 | 476–483 W | 41.0 klm | 46.6 klm |
| 18 | 571–580 W | 49.1 klm | 55.9 klm |

This is electrically scalable, but a 12–18 COB cluster is mechanically
unattractive for a compact Bowens head: the effective emitting area becomes
large, thermal interfaces multiply, current sharing becomes more complex, and
narrower optical modifiers become harder to couple efficiently.

The Thrive cluster remains valuable as:

- a spectral-quality reference;
- a fallback architecture based on broadly documented parts;
- a way to prototype lower-power configurations;
- a benchmark for comparing high-power integrated matrices.

It is no longer the preferred first high-power prototype architecture.

## 4. Purpose-built high-power tunable-white matrices

Yujileds publishes bicolor LED Matrix modules specifically targeted at
photographic, film, studio, stage, and entertainment lighting.

### 4.1 300 W-class matrix

The Yujileds B3240003.26 datasheet labels this package:

> Chip-on-Board bicolor / Rated 300W; Max 720W / Bicolor

At the published rated optical test point for each endpoint:

| Parameter | 2700 K | 6500 K |
|---|---:|---:|
| Test current | 4.0 A | 4.0 A |
| Forward-voltage range | 33–41 V | 33–41 V |
| Luminous flux | 10,800 lm | 13,800 lm |
| Ra | >=95 | 95 typical |
| R9 | 90 | 90 |
| TM-30 Rf / Rg | 92 / 100 | 92 / 100 |
| TLCI | 97 | 97 |
| SSI | 87 | 67 |

The corresponding electrical power at the stated endpoint test condition is
approximately **132–164 W for the active endpoint channel**.

The absolute-maximum table lists 9 A and 360 W, while the bicolor section title
states 720 W maximum. These figures strongly suggest channel-level versus
combined-package limits, but the datasheet text does not state a clear
continuous **WW + CW simultaneous-current envelope**. That point must be
confirmed with Yujileds before a production driver is designed.

The mechanical drawing shows a board approximately **70 x 60 mm** with a
central **40 mm diameter emitting region** and separate WW/CW terminals. This is
far more compact optically than a many-COB cluster.

### 4.2 500 W-class matrix

The Yujileds B3240005.26 datasheet labels this package:

> Chip-on-Board bicolor / Rated 500W; Max 1440W / Bicolor

At the published endpoint test condition:

| Parameter | 2700 K | 6500 K |
|---|---:|---:|
| Test current | 8.4 A | 8.4 A |
| Forward-voltage range | 33–41 V | 33–41 V |
| Luminous flux | 20,000 lm | 25,600 lm |
| Ra | >=95 | 95 typical |
| R9 | 90 | 90 |
| TM-30 Rf / Rg | 92 / 100 | 92 / 100 |
| TLCI | 97 | 97 |
| SSI | 87 | 67 |

The endpoint test condition corresponds to roughly **277–344 W on the active
channel** from the published current and voltage range.

Its absolute-maximum table lists 18 A and 720 W while the bicolor section title
states 1440 W maximum. As with the 300 W-class part, the combined continuous
thermal/current envelope must be clarified with the manufacturer instead of
being inferred from the section title.

### 4.3 Why these matrices change the prototype strategy

These parts demonstrate that a high-output, high-CRI bicolor engine can be
implemented as one purpose-built optical matrix rather than a ring or cluster of
many general-purpose COBs.

That has several advantages for this project:

- much smaller effective source geometry;
- warm and cool dies are physically interleaved by the module manufacturer;
- fewer high-current interfaces;
- simpler Bowens coupling;
- one thermal interface rather than many;
- published R9, TM-30, TLCI, and SSI data relevant to camera use.

The trade-off is dependence on a more specialized supplier and the need to
clarify availability, price, lifetime data, and combined-channel operating
limits.

## 5. Power-envelope interpretation

The present candidates should be treated as two different design envelopes:

| Envelope | Candidate direction | Expected role |
|---|---|---|
| ~150–250 W endpoint LED power | 300 W-class integrated matrix or ~6 Thrive COBs | Normal 90 cm softbox use; likely practical key/fill class |
| ~280–350 W endpoint LED power | 500 W-class integrated matrix or ~9–12 Thrive COBs | Stronger head; candidate for approaching the large-modifier limit |
| ~400–600 W endpoint LED power | large COB cluster or higher-current matrix operation only after validated limits | Current 1200 lx + grid ceiling if lower-power tests are insufficient |

The table deliberately uses **endpoint LED electrical power**, not marketing
fixture wattage. A bicolor engine may have different total power at endpoints
and intermediate CCTs depending on the allowed combined-current policy.

No DC-bus voltage is frozen. For scale only, if a future driver were 90 %
efficient and used a 48 V bus:

```text
300 W LED load -> ~333 W from DC bus -> ~6.9 A at 48 V
500 W LED load -> ~556 W from DC bus -> ~11.6 A at 48 V
600 W LED load -> ~667 W from DC bus -> ~13.9 A at 48 V
```

These are placeholders for connector, cable, and supply-envelope thinking only.
The actual driver efficiency, bus voltage, and head power must come from the
selected topology.

A 48 V / 15 A external supply architecture is technically plausible at this
scale: for comparison, Aputure specifies 48 V / 15 A DC input for the LS 600x
Pro. This comparison does **not** freeze 48 V for OpenSpectralLight.

## 6. Thermal implication

Externalizing the AC/DC supply removes power-supply heat and weight from the
lamp head, but it does not remove the dominant LED heat load.

At approximately 300–500 W LED electrical input, a compact passive-only head is
not a realistic default assumption. A large passive heat spreader remains
necessary, but the prototype should assume **temperature-controlled forced
airflow is likely** and optimize it acoustically.

The commercial LS 600x Pro is also specified as active-cooled. This does not
define our fan design, but it is a useful sanity check on the thermal scale.

Thermal characterization must determine:

- case/board temperature at each endpoint and intermediate CCT;
- heat-sink inlet/outlet temperature;
- fan speed needed for steady state;
- optical output drift during warm-up;
- CCT/Duv drift during warm-up;
- acoustic noise at the intended 1–2 m shooting distance.

## 7. Revised prototype direction

The first high-power optical prototype should **not** start by assembling a
12–18 COB cluster.

The preferred sequence is now:

1. verify price, availability, current datasheet revision, and supplier support
   for the Yujileds 300 W- and 500 W-class bicolor matrices;
2. obtain an explicit manufacturer answer for the safe continuous combined WW/CW
   current/power envelope;
3. bench the smaller practical matrix first if procurement permits, using a
   laboratory constant-current setup and oversized thermal solution;
4. measure bare-source and modifier output at 2700 K, approximately 4300 K, and
   6500 K;
5. repeat with the 90 cm normal modifier and the 120 cm limit modifier, with and
   without grid;
6. measure SPD, CCT, Duv, output, and temperature from cold start to thermal
   equilibrium;
7. determine whether the 300 W-class engine already covers the real use case or
   whether the 500 W-class engine is justified;
8. retain the Bridgelux Thrive cluster as the quality/fallback reference.

This sequence directly tests whether the 1200 lx limit target justifies the
thermal and acoustic cost of a larger light engine.

## 8. Decision gate

Do **not** freeze the LED engine, driver, DC connector, or heatsink until the
following are known:

- actual modifier transmission for the selected 90 cm and 120 cm units;
- measured 2 m illuminance from at least one high-power matrix;
- output versus CCT;
- Duv/mixing trajectory;
- steady-state thermal behavior;
- acoustic consequence of required airflow;
- procurement cost and repeatable availability;
- confirmed combined-channel electrical limits.

If a 300 W-class implementation comfortably covers normal key-light use and the
500 W-class implementation only exists to satisfy an unnecessarily harsh limit
case, the product requirement should be revisited rather than automatically
building the larger fixture.

## References

1. Aputure, **Light OctaDome 120**.
   https://aputure.com/EN-US/products/light-octadome-120
2. Aputure, **LS 600x Pro**.
   https://aputure.com/en-US/products/ls-600x-pro
3. Bridgelux, **Vesta Series Tunable White 18 mm Gen 2, DS353 Rev K**.
   https://www.bridgelux.com/sites/default/files/2024-10/ds353_bridgelux_vesta_series_tunable_white_18mm_array_gen_2_data_sheet_202409_rev_k.pdf
4. Yujileds, **LED Matrix Solution Introduction & Datasheet, V1.5**.
   https://www.yujiintl.com/wp-content/uploads/2022/09/Yujileds-LED-Matrix-Solution-V1.5.pdf
5. Yujileds, **Tunable White (300W-720W)**.
   https://www.yujiintl.com/tunable-white-300w-720w/
6. Yujileds, **Tunable White (500W-1440W)**.
   https://www.yujiintl.com/tunable-white-500w-1440w/
