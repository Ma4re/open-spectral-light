# Phase 1 Tunable-White Product Requirements

## 1. Purpose

Phase 1 defines a high-quality continuous tunable-white lighting head for
photography and video. Its primary use is as a **key light** or **fill light**
for portraits, close shots, medium shots, interviews, and music-video scenes.

The product is not intended to compete on maximum electrical wattage. Optical
performance is specified from the light delivered to the subject under defined
measurement conditions.

The base product must already be useful as a serious photographic/video light.
Future premium modules may add capabilities, but core light quality must not
depend on premium hardware.

## 2. Requirement states

Requirements use three states:

- **FROZEN** — accepted product requirement; design work may rely on it.
- **TARGET** — preferred engineering target that still requires validation or
  trade-off analysis before becoming frozen.
- **OPEN** — intentionally unresolved; additional analysis, measurement, or
  component comparison is required.

A TARGET is not a guaranteed specification.

## 3. Intended use

| ID | State | Requirement |
|---|---|---|
| USE-001 | FROZEN | The light shall support continuous-light photography and normal-speed video production. |
| USE-002 | FROZEN | Primary roles are key light and fill light. |
| USE-003 | FROZEN | Primary framing is portrait, close shot, and medium/half-body shot. Full-body coverage is not a Phase 1 design target. |
| USE-004 | FROZEN | Normal working distance is approximately 1–2 m from the subject. |
| USE-005 | FROZEN | The light shall be suitable for cameras and smartphones. |
| USE-006 | FROZEN | Typical creative use includes portrait photography and music-video/cinematic scenes. |

## 4. Optical and modifier requirements

| ID | State | Requirement |
|---|---|---|
| OPT-001 | FROZEN | The front modifier interface shall use Bowens S-mount. |
| OPT-002 | FROZEN | The light engine and mechanical layout shall support practical use with softboxes rather than being optimized only for a bare-source lux figure. |
| OPT-003 | TARGET | Phase 1 should support a useful CCT range of approximately **2700–6500 K**. This range includes conventional tungsten-like and daylight-like working points without prioritizing extreme CCT range over output or spectral quality. |
| OPT-004 | OPEN | Final CCT endpoints shall be frozen only after emitter/channel architecture is evaluated for output, tint control, efficiency, and color quality across the range. |
| OPT-005 | FROZEN | Electrical input wattage shall not be used as the primary optical-performance requirement. |
| OPT-006 | TARGET | The initial upper output target is **1200 lx at the subject plane** through the limit reference modifier at **2 m**, measured on axis with a flat illuminance detector normal to the fixture centerline. |
| OPT-007 | FROZEN | Modifier validation shall include a normal **~90 cm Bowens octabox/deep softbox with double diffusion** and a limit **~120 cm Bowens parabolic/octabox with double diffusion and grid**. Exact reference products remain OPEN until test hardware is selected. |
| OPT-008 | TARGET | Output should remain useful as a key light at 2 m for the intended close/medium-shot use case, not merely as a fill light. |
| OPT-009 | OPEN | Spatial-uniformity and angular color-uniformity limits shall be defined after the optical mixing architecture is selected. |
| OPT-010 | FROZEN | Published output validation shall state modifier, diffusion layers, grid state, distance measured from the front diffusion plane, CCT, and detector orientation. |

The 1200 lx target is derived from realistic camera exposure rather than from
competitor electrical wattage. It is intentionally an engineering target, not a
guaranteed product specification. It must be rechecked after the actual light
engine and reference modifier are characterized.

## 5. Color-quality and upgrade requirements

| ID | State | Requirement |
|---|---|---|
| COL-001 | FROZEN | The base light shall prioritize natural, consistent skin rendering on camera. |
| COL-002 | FROZEN | The architecture shall preserve measured/calibrated output as distinct from requested CCT or channel setpoints, following `../05-color-science.md`. |
| COL-003 | FROZEN | The system shall not assume that CCT alone completely describes the emitted spectrum or white-point quality. |
| COL-004 | TARGET | The light-engine architecture should allow future finer tint/Duv control without requiring replacement of the controller architecture. |
| COL-005 | TARGET | The modular architecture should permit future additional spectral channels and/or a spectral-sensing module when those features justify their cost. |
| COL-006 | OPEN | Numerical CRI, R9, TM-30, SSI, Duv, or similar acceptance limits shall be frozen only after the emitter architecture and available measurement chain are evaluated. |

Premium capability means **additional controllability, sensing, calibration, or
spectral flexibility**. It must not be used to excuse poor base light quality.

## 6. Dimming and temporal behavior

| ID | State | Requirement |
|---|---|---|
| TMP-001 | FROZEN | Phase 1 requires manual intensity adjustment; programmed fades and lighting effects are not required. |
| TMP-002 | TARGET | Dimming shall be visually smooth and provide useful low-output control without obvious step changes. |
| TMP-003 | OPEN | Minimum stable continuous-current dimming level and final control resolution shall be derived from emitter/driver characterization. |
| TMP-004 | FROZEN | The light shall support normal video acquisition up to 60 fps without visible brightness or color banding under the validated operating matrix. |
| TMP-005 | FROZEN | The Phase 1 creative acceptance baseline shall cover approximately 24 fps / 1/48 s, 25 fps / 1/50 s, 30 fps / 1/60 s, 50 fps / 1/100 s, and 60 fps / 1/120 s. |
| TMP-006 | FROZEN | Frame rate alone shall not be treated as sufficient evidence of flicker-free behavior; exposure time and rolling-shutter interaction must also be considered. |
| TMP-007 | FROZEN | Normal tunable-white operation shall not rely on time-division alternation of warm and cool channels as the default method for synthesizing CCT. The intended spectral mix should be emitted concurrently. |
| TMP-008 | TARGET | Continuous-current/amplitude dimming should cover as much of the principal video operating range as practical. Any PWM or hybrid deep-dimming region shall be optically measured and camera-validated. |
| TMP-009 | TARGET | Robustness characterization shall probe faster shutter times around 1/250 s, 1/500 s, and 1/1000 s to identify the temporal limit; these points are not yet guaranteed product specifications. |
| TMP-010 | TARGET | If PWM is used while both white channels are active, synchronized/common temporal gating is preferred so the instantaneous WW/CW spectral ratio is preserved. |

The temporal requirements are grounded by
[`../theory/temporal-light-modulation.md`](../theory/temporal-light-modulation.md)
and [`../theory/camera-interaction.md`](../theory/camera-interaction.md).

120 fps is not a Phase 1 creative-use requirement. It may still be exercised in
engineering characterization if useful, but the product is not being designed
around slow-motion production.

## 7. Power architecture

| ID | State | Requirement |
|---|---|---|
| PWR-001 | FROZEN | Mains AC conversion shall remain external to the lighting head in Phase 1. |
| PWR-002 | FROZEN | The lighting head shall accept a defined external DC input. |
| PWR-003 | TARGET | The external-DC architecture should allow operation from either an AC/DC supply or a compatible battery solution without changing the lighting head. |
| PWR-004 | FROZEN | An external source is compatible only when it satisfies the defined voltage, current capability, polarity, protection, and connector requirements; arbitrary power sources shall not be assumed safe or compatible. |
| PWR-005 | OPEN | DC input voltage, maximum current, connector, polarity convention, and protection strategy remain to be selected from the final power requirement. |

A DC-005/barrel-style connector is therefore **not frozen**. The connector must
be chosen after maximum voltage/current and mechanical requirements are known;
the preferred architecture is external DC power, not a particular connector.

## 8. Thermal and acoustic requirements

| ID | State | Requirement |
|---|---|---|
| THM-001 | FROZEN | The lighting head shall include a deliberate thermal path from emitters and power electronics to the enclosure/heatsink system. |
| THM-002 | FROZEN | Externalizing the AC/DC supply is preferred partly to reduce mass and internally generated heat in the lighting head. |
| THM-003 | TARGET | Passive cooling should be used as far as practical before adding forced airflow. |
| THM-004 | TARGET | If a fan is required, it should be temperature-controlled and selected/operated for low acoustic noise rather than maximum airflow by default. |
| THM-005 | OPEN | Maximum LED junction/board temperature, heatsink temperature, derating policy, fan thresholds, and allowable acoustic noise shall be established after the light-engine power target is known. |
| THM-006 | FROZEN | Thermal design must maintain safe and repeatable optical operation during sustained continuous-light use. |

## 9. Mechanical requirements

| ID | State | Requirement |
|---|---|---|
| MEC-001 | FROZEN | The lighting head should be robust enough for repeated indoor photographic/video use without becoming unnecessarily heavy or bulky. |
| MEC-002 | FROZEN | The AC/DC supply is external to reduce head weight and thermal load. |
| MEC-003 | TARGET | The head should use a yoke or equivalent tilt mechanism suitable for mounting on a conventional lighting stand. |
| MEC-004 | TARGET | The stand interface should use the common 5/8-inch (approximately 16 mm) lighting spigot/baby-pin ecosystem unless mechanical design reveals a better compatible solution. |
| MEC-005 | OPEN | Head dimensions, mass target, yoke geometry, center of gravity, and softbox load limits remain to be established. |

A **light stand** is the support/tripod used to position studio lighting. The
lighting head normally attaches through a yoke or bracket to a standard spigot,
allowing height and tilt adjustment independently of the Bowens modifier mount.

## 10. Local controls

| ID | State | Requirement |
|---|---|---|
| CTL-001 | FROZEN | The light must be fully operable from local controls. |
| CTL-002 | TARGET | A rotary encoder with push action is the preferred primary control for Phase 1. |
| CTL-003 | FROZEN | At minimum, local control shall expose output intensity and CCT. |
| CTL-004 | OPEN | Whether dedicated buttons are justified for navigation, back/home, presets, or rapid parameter access shall be decided after the UI parameter set is defined. |
| CTL-005 | TARGET | The local UI architecture should be able to expose future parameters such as tint/Duv or additional-channel controls without redesigning the entire controller. |

## 11. Cost and modularity

| ID | State | Requirement |
|---|---|---|
| CST-001 | FROZEN | The design shall balance cost and quality rather than optimize for the minimum BOM cost or commercial high-end feature count. |
| CST-002 | FROZEN | Components shall be selected for meaningful optical, electrical, thermal, reliability, or usability benefit; expensive parts shall not be chosen solely because they are premium. |
| CST-003 | FROZEN | The architecture shall permit a lower-cost useful base configuration and incremental capability upgrades where technically sensible. |
| CST-004 | FROZEN | Optional premium modules shall not be required for safe basic light operation. |

## 12. Supply-chain and serviceability requirements

| ID | State | Requirement |
|---|---|---|
| SUP-001 | FROZEN | The primary Phase 1 emitter path shall not require direct manufacturer sales or engineering response in order to purchase prototype quantities. |
| SUP-002 | FROZEN | Primary emitters shall have public datasheets, identifiable lifecycle status, and an authorized-distributor procurement path. |
| SUP-003 | TARGET | Prefer emitter families supported by two or more major authorized distributors or by a replaceable light-engine module that can accept an alternate qualified family. |
| SUP-004 | FROZEN | A direct-vendor-only custom LED matrix may be used as a benchmark or optional experiment, but shall not be the only viable base light-engine source. |
| SUP-005 | FROZEN | The light engine shall be a replaceable module whose vendor-specific emitter geometry is isolated behind stable mechanical, thermal, optical, power, and identification/sensing interfaces. |
| SUP-006 | FROZEN | Replacing a compatible two-channel light engine within the accepted electrical envelope shall not require redesign of the controller, user interface, or product-level firmware behavior. |
| SUP-007 | FROZEN | If a future light engine exceeds the existing power-stage voltage/current/channel envelope, the power-stage module may be replaced together with the light engine while preserving the controller boundary. |
| SUP-008 | TARGET | The replaceable light engine should carry its own module identity and calibration data so replacement does not require hard-coded vendor-specific controller firmware. |
| SUP-009 | FROZEN | Phase 1 light-engine replacement is a powered-off service operation; hot-swap support is not required. |

Supply-chain independence is part of product maintainability. Optical performance
still governs acceptance, but a technically excellent emitter that cannot be
ordered or supported predictably is not a suitable sole foundation for the
base product.

## 13. Reference exposure model and validation scenarios

### 13.1 Exposure basis

For the first illuminance target, use the incident-light exposure relationship

```math
E = \frac{C N^2}{t S},
```

where `E` is illuminance in lux, `N` is f-number, `t` is exposure time in
seconds, `S` is ISO arithmetic speed, and `C` is the incident-meter calibration
constant. A **flat receptor value of C = 250** is used so the calculation maps to
a reproducible planar illuminance measurement rather than a hemispherical
portrait-meter reading.

The design allowance is **+1 stop of illuminance headroom**, so the engineering
target is twice the calculated exposure minimum.

| Video case | Exposure | Minimum illuminance | +1 stop design target |
|---|---|---:|---:|
| 24 fps, 180° | f/2.8, ISO 400, 1/48 s | 235 lx | 470 lx |
| 25 fps, 180° | f/2.8, ISO 400, 1/50 s | 245 lx | 490 lx |
| 30 fps, 180° | f/2.8, ISO 400, 1/60 s | 294 lx | 588 lx |
| 24 fps, 180° | f/4, ISO 400, 1/48 s | 480 lx | 960 lx |
| 25 fps, 180° | f/4, ISO 400, 1/50 s | 500 lx | 1000 lx |
| 30 fps, 180° | f/4, ISO 400, 1/60 s | 600 lx | **1200 lx** |
| 50 fps, 180° | f/2.8, ISO 400, 1/100 s | 490 lx | 980 lx |
| 60 fps, 180° | f/2.8, ISO 400, 1/120 s | 588 lx | **1176 lx** |

The two most demanding Phase 1 creative cases therefore converge near **1200 lx**:
30 fps at f/4 and 60 fps at f/2.8, both at ISO 400 with one stop of reserve. Phase
1 does **not** require f/4 at 50/60 fps; doing so would raise the target toward
2000–2400 lx and materially increase light-engine and thermal requirements for a
use case outside the agreed priority.

This relationship establishes a repeatable engineering baseline. Real cameras
may differ due to ISO calibration, lens transmission (T-stop versus f-stop),
picture profile, desired highlight margin, and artistic exposure. Final validation
therefore includes both photometric measurement and real camera tests.

### V1 — Normal key light

- nominal 90 cm Bowens octabox/deep softbox;
- double diffusion;
- no grid;
- 1–1.5 m from the front diffusion plane to the subject;
- portrait/close or half-body framing;
- 24/25/30 fps;
- f/2.8–f/4 around ISO 400.

### V2 — Limit key-light case

- nominal 120 cm Bowens parabolic/octabox;
- double diffusion plus grid;
- 2 m from the front diffusion plane to the subject;
- portrait/half-body framing;
- 60 fps, 1/120 s, f/2.8, ISO 400;
- **TARGET: at least 1200 lx on axis at the subject plane**, providing approximately one stop of margin over the calculated exposure minimum.

### V3 — Fill light

- 1–2 m working distance;
- dimmed output;
- smooth low-level control without objectionable color or temporal instability.

### V4 — Normal-speed video

- cameras and smartphones;
- 24/25/30/50/60 fps as applicable;
- 180°-equivalent exposure times plus selected faster shutter cases;
- no visible temporal artefacts within the validated matrix.

### V5 — Sustained operation

- continuous output at a defined high-load operating point;
- thermal equilibrium reached;
- no unsafe temperature, uncontrolled output drift, or unacceptable acoustic behavior.

### 13.2 Reference-method notes

A 90 cm circular Bowens softbox and larger 120 cm parabolic softboxes are common
real-world modifier classes. Commercial examples also use inner/front diffusion
and optional fabric grids, so these scenarios are intentionally representative
of practical portrait/video use rather than bare-reflector photometrics.

The reference modifier brand/model remains OPEN because different fabrics,
depths, reflective interiors, inner baffles, and grids have different optical
losses. The actual Phase 1 acceptance test must use one documented physical
modifier so later measurements are repeatable.

## 14. Open engineering questions

The next work should resolve these questions in roughly this order:

1. Is **1200 lx at 2 m through the limit modifier** achievable at a sensible electrical/thermal cost, or does the modifier/output trade-off need adjustment?
2. Is 2700–6500 K the best useful range after emitter-efficiency and color-quality trade-offs are considered?
3. What emitter/channel architecture provides high-quality tunable white while preserving a path to tint/spectral expansion?
4. What current-regulation/power-stage topology best implements the accepted temporal requirements over the required WW/CW current range?
5. What electrical power follows from the 1200 lx limit-case target?
6. What external DC voltage and connector are appropriate at that power level?
7. What thermal architecture and acoustic target follow from sustained power dissipation?
8. Which local parameters actually exist in Phase 1, and does the encoder require supporting buttons?

These questions must be answered before individual LED, driver, connector, fan,
or MCU part numbers are frozen.

## 15. References for the exposure baseline

1. ISO, **ISO 2720:1974 — Photography — General purpose photographic exposure
   meters (photoelectric type) — Guide to product specification**. The standard
   was reviewed and confirmed in 2026.
   https://www.iso.org/standard/7690.html
2. Sekonic, **L-758 Operating Manual**, technical data: incident-light
   calibration constants `C = 340` for the Lumisphere and `C = 250` for the
   flat diffuser.
   https://sekonic.com/content/Files/manual/L-758/l-758_operating_manual_en.pdf
3. Aputure, **Light Dome III**, 90 cm Bowens softbox with diffusion options and
   fabric light-control grid.
   https://aputure.com/en-US/products/light-dome-iii
4. Godox, **QR-P Series Quick Release Parabolic Softbox**, including 120 cm-class
   Bowens modifiers with inner/front diffusion and optional grids.
   https://www.godox.com/product-d/QR-Series.html
