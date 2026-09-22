# Prototype A — Split-White Thrive Light Engine

## 1. Purpose

Prototype A is the first concrete Light Engine Module (LEM) used to establish
the numerical envelope for the Phase 1 LEM-TW2 interface.

It uses standard distributor-backed single-CCT COBs rather than a custom
high-power bicolor matrix.

This document does not freeze Bridgelux as the final emitter vendor. The
prototype exists to establish realistic channel power, branch current/voltage,
source geometry, thermal-plane size, optical-mixing requirements, and module
identity/temperature needs.

## 2. Selected prototype emitters

Prototype A uses Bridgelux Gen 7 V18 Thrive COBs as a high-fidelity reference:

- warm bank: BXRE-27S4001-C-73, 2700 K;
- cool bank: BXRE-65S4001-C-74, 6500 K.

Current distributor data list both parts as active and available in prototype
quantities.

Relevant published characteristics for the V18 class include:

| Parameter | Value |
|---|---:|
| Package | 24 x 24 mm |
| LES | approximately 19.2 mm diameter |
| Typical Vf at 1.17 A | approximately 34.4 V at 25 C |
| Typical stabilized Vf at 1.17 A | approximately 33.7 V at Tc = 85 C |
| Nominal current | 1.17 A |
| Typical nominal electrical power | approximately 40 W |
| Maximum continuous current | 2.34 A |
| Typical Vf at 2.34 A | approximately 37.5 V |
| Cold driver-selection maximum at 2.34 A | approximately 41.2 V |
| Maximum junction temperature | 150 C |
| Maximum operating case temperature | 105 C |
| Typical Rj-c | approximately 0.11 C/W at nominal current; approximately 0.13 C/W at 2.34 A |

The exact CRI/Thrive labeling varies between current catalog/distributor
presentation and datasheet revisions. Prototype measurements, not the marketing
label alone, will establish the actual spectral/color performance of the
purchased samples.

## 3. COB-count comparison

Three symmetric configurations were considered.

| Configuration | COBs | WW + CW | Maximum electrical power at one CCT endpoint | Main issue |
|---|---:|---:|---:|---|
| 3 + 3 | 6 | 3 WW + 3 CW | approximately 250 W | No useful headroom for a 300 W prototype |
| 4 + 4 | 8 | 4 WW + 4 CW | approximately 334 W | Good balance of power, symmetry, cost, and source size |
| 5 + 5 | 10 | 5 WW + 5 CW | approximately 418 W | Larger source and thermal plane than needed for the first prototype |

Prototype A therefore uses 4 WW + 4 CW COBs.

## 4. Operating-power target

The prototype uses a 300 W total LEM operating ceiling as the initial
characterization target.

At a pure endpoint, only one color bank must carry the full module output.
A 300 W bank therefore requires approximately 75 W per active COB.

Interpolating the published V18 performance table gives approximately:

- I_COB: 2.11 A
- Vf_COB: 35.5 V typical
- P_COB: 75 W

This leaves current margin below the 2.34 A maximum.

At the absolute maximum of 2.34 A, one 4-COB bank would be approximately
334 W electrical. The additional luminous output over the 300 W operating point
is small enough that the extra thermal stress is not attractive for normal
operation.

The 300 W ceiling is a prototype TARGET, not yet a frozen product rating.

## 5. First-order endpoint optical output

Using the datasheet's typical stabilized DC flux at Tc = 85 C and interpolating
between the published 1.755 A and 2.34 A points:

### 2700 K endpoint at approximately 300 W

Per warm COB:

- approximately 2.11 A;
- approximately 75 W;
- approximately 5.94 klm typical stabilized DC.

Four warm COBs provide approximately 23.8 klm typical stabilized DC.

### 6500 K endpoint at approximately 300 W

Per cool COB:

- approximately 2.11 A;
- approximately 75 W;
- approximately 7.11 klm typical stabilized DC.

Four cool COBs provide approximately 28.5 klm typical stabilized DC.

The cool endpoint therefore does not need the full 300 W if the product chooses
to keep endpoint luminous output approximately constant.

A first-order interpolation indicates that the cool bank reaches about the same
23.8 klm near approximately 1.52 A and 53 W per cool COB, or about 212 W total.

This demonstrates an important control principle:

- module electrical power is a ceiling, not the user-visible intensity model;
- per-channel current must be calibrated versus optical output;
- equal current or equal electrical power shall not be assumed to produce equal
  brightness across CCT.

These lumen estimates are not substitutes for lux-through-modifier testing or
camera exposure validation.

## 6. Mixed-CCT operation

At intermediate CCT both banks operate concurrently.

The controller shall enforce P_WW + P_CW <= P_LEM_limit in addition to each
branch's current limit.

A simple equal-current WW/CW mixture is not assumed to correspond to any
particular Kelvin value. Actual current mappings will come from measured
SPD/chromaticity and output data.

The 4+4 architecture intentionally provides more installed electrical capability
than the 300 W total-module ceiling so current can be redistributed between WW
and CW across the CCT range.

## 7. Physical emitter layout

The preferred first layout is a regular octagonal ring with alternating CCT:

~~~text
              WW
        CW          CW

     WW                WW

        CW          CW
              WW
~~~

The exact drawing will use equal-radius emitter centers and alternate WW/CW
around the ring.

Advantages:

- both CCT banks have the same radial distribution;
- no bank is concentrated only at the center or only at the perimeter;
- symmetry simplifies spatial-color characterization;
- the central region remains available for a mixing feature or mechanical
  structure.

For 24 mm square COBs, an initial center-to-center spacing of roughly 28–30 mm
implies an emitter-center radius of roughly 37–39 mm and a worst-case physical
source/carrier envelope around 107–112 mm before mounting margins.

These are prototype geometry estimates, not the standardized LEM mechanical
interface.

## 8. Bowens and local mixing consequence

Common Bowens S-mount accessory openings are around the same scale as the raw
eight-COB source envelope.

Therefore Prototype A should not assume each COB has a direct unobstructed view
through the modifier opening.

Instead the LEM should test a short local mixing structure that:

- collects light from the octagonal COB ring;
- reduces direct visibility of individual warm/cool sources;
- presents a smaller, approximately centered output aperture to the head optics;
- avoids contact with the COB LES;
- minimizes unnecessary absorption.

An initial output-aperture target below approximately 90 mm is reasonable for
prototype geometry work, but its exact diameter remains OPEN until a physical
Bowens interface is measured and the optical losses are characterized.

The mixing structure may use diffuse reflective walls and/or a removable
diffuser. It is part of the LEM when needed specifically to correct split-white
source geometry.

## 9. Prototype carrier envelope

A practical first carrier should reserve roughly 120–130 mm laterally. This
leaves room for the approximately 110 mm emitter envelope, COB mounting
features, temperature sensors, local wiring, module ID/calibration memory, and
mounting margin.

This does not freeze the final LEM mounting plane. The first bench carrier may be
larger if required for thermal testing.

## 10. Electrical branch topology

Directly placing four high-power COBs in parallel behind one current regulator is
not preferred because LED forward-voltage spread and negative temperature
coefficient can produce unequal current sharing.

Prototype A should therefore not define one raw 8–9 A parallel LED load per CCT
bank.

The preferred bench topology is:

- four independently current-regulated WW branches;
- four independently current-regulated CW branches;
- all four branches in one bank receive a common logical bank-current command;
- per-branch trim/telemetry may be used for characterization.

The controller still sees only two logical light channels: WW bank and CW bank.
The branch implementation belongs below the product-control layer.

At the 300 W endpoint:

- 4 active branches x approximately 2.11 A each;
- branch Vf approximately 35–41 V design range;
- bank electrical ceiling approximately 300 W.

This topology is naturally compatible with a future approximately 48 V DC bus
and buck-style branch regulation, but 48 V and the driver topology remain OPEN.

## 11. Alternative string topology kept for comparison

A 2-series / 2-parallel-string arrangement per color would reduce current-
regulator count but raises the LED string voltage toward approximately 70–82 V.

That would require a higher-voltage bus or boost/buck-boost conversion and
changes the safety/battery trade-off.

It remains an architecture comparison point, not the Prototype A baseline.

A 4-series string per color is not preferred for Phase 1 because it would push
the LED string well above 100 V and unnecessarily complicate the external-DC
architecture.

## 12. Temperature sensing

Because pure-CCT endpoint operation loads only one bank heavily, one center
temperature sensor is not sufficient to represent both banks reliably.

Prototype A should include at least:

- one analog/passive temperature sensor thermally close to a representative WW
  COB;
- one analog/passive temperature sensor thermally close to a representative CW
  COB.

These become logical TEMP_WW and TEMP_CW inputs for the LEM-TW2 study.

The exact sensor part and resistance curve remain OPEN.

## 13. Module identity and calibration memory

Prototype A should include the first implementation of the LEM nonvolatile
descriptor/calibration concept.

The exact memory IC/bus is still OPEN, but the prototype carrier should reserve
space and low-voltage connectivity for module identity, interface version,
branch count, safe current/power limits, temperature-sensor definition, and
optical calibration data.

The first bench revision may operate without final production firmware support,
but the hardware should make the concept testable.

## 14. Preliminary LEM-TW2 numerical envelope

The first prototype establishes the following candidate, not frozen, interface
envelope:

| Property | Prototype A candidate |
|---|---|
| Logical light channels | 2: WW, CW |
| Physical branches | 4 WW + 4 CW |
| COB branch forward-voltage design range | approximately 31–42 V |
| Branch normal prototype current | 0–approximately 2.11 A, subject to low-current characterization |
| Branch absolute component limit | 2.34 A |
| Total LEM operating ceiling | approximately 300 W TARGET |
| One-bank installed capability | approximately 334 W component-level maximum scale |
| Physical raw emitter envelope | approximately 110 mm |
| Prototype carrier/spreader envelope | approximately 120–130 mm |
| Temperature inputs | at least 2 analog paths: WW + CW |
| Module identity/calibration | nonvolatile memory reserved |
| Hot swap | not supported |

The final standardized interface must leave adequate electrical and thermal
margin and must not copy these prototype values blindly.

## 15. Why 4 + 4 is preferred

The 4+4 topology is the smallest symmetric configuration that:

- can reach a genuine approximately 300 W endpoint without exceeding COB current
  limits;
- maintains identical spatial emitter count for warm and cool banks;
- provides power headroom for CCT-dependent current redistribution;
- uses currently orderable standard COBs;
- keeps the emitter BOM near the low-hundreds-of-dollars scale for a complete
  prototype rather than requiring a custom high-power matrix;
- gives the project enough power to test whether the demanding 1200 lx limit
  target is realistic.

Adding a fifth COB per bank before those measurements would increase source area
and cooling requirements without answering a current engineering need.

## 16. Prototype-A decision gates

Before the carrier becomes a hardware revision, verify:

1. exact purchased COB datasheet revision and CCT bin;
2. current distributor lifecycle/stock for both endpoint parts;
3. final octagonal pitch with real COB mounting clearances;
4. actual Bowens clear aperture on the selected head/mount hardware;
5. candidate local mixing-chamber geometry;
6. thermal-spreader material/thickness from a first thermal model;
7. physical current-branch implementation;
8. connector strategy between PSM and LEM;
9. temperature-sensor placement and fault behavior;
10. first module-descriptor memory implementation.

## References

1. Bridgelux, Gen 7 V18 Thrive Array, DS322 / Thrive product family.
   https://www.bridgelux.com/thrive
2. Digi-Key, BXRE-27S4001-C-73.
   https://www.digikey.com/en/products/detail/bridgelux/BXRE-27S4001-C-73/11203640
3. Digi-Key, BXRE-65S4001-C-74.
   https://www.digikey.com/en/products/detail/bridgelux/BXRE-65S4001-C-74/11203636
4. Bresser, Bowens S-bayonet accessory dimensions, used only as a practical
   reference for approximate modifier-opening scale.
   https://www.bresser.com/p/bresser-ad-3-universal-mini-adapter-with-bowens-F000386
