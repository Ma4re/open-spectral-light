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
| OPT-006 | OPEN | Required illuminance at the subject shall be derived from realistic camera exposure scenarios and measured through a defined reference softbox at 1 m and 2 m. |
| OPT-007 | OPEN | Reference softbox size and geometry for performance validation shall be selected before publishing illuminance targets. |
| OPT-008 | TARGET | Output should remain useful as a key light at 2 m for the intended close/medium-shot use case, not merely as a fill light. |
| OPT-009 | OPEN | Spatial-uniformity and angular color-uniformity limits shall be defined after the optical mixing architecture is selected. |

The illuminance target will be derived from the desired photographic exposure,
not chosen from a nominal competitor wattage. Candidate exposure cases should
cover normal video shutter settings and practical apertures/ISO values for
indoor portrait and music-video work.

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
| TMP-003 | OPEN | Minimum stable dimming level and control resolution shall be derived from driver topology and visual/camera validation. |
| TMP-004 | FROZEN | The light shall support normal video acquisition up to 60 fps without visible flicker or banding under the validated operating matrix. |
| TMP-005 | OPEN | The exact shutter-speed/exposure-time validation matrix shall be defined after `temporal-light-modulation.md` and `camera-interaction.md` are completed. |
| TMP-006 | FROZEN | Frame rate alone shall not be treated as sufficient evidence of flicker-free behavior; exposure time and rolling-shutter interaction must also be considered. |

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

## 12. Phase 1 validation scenarios

The following scenarios define the direction of later quantitative testing. Exact
acceptance values remain OPEN until the relevant theory and component studies are
complete.

### V1 — Key light, close/medium portrait

- subject distance: 1 m;
- Bowens softbox installed;
- portrait/close or half-body framing;
- output sufficient for practical camera exposure without requiring extreme ISO.

### V2 — Key light at room-scale distance

- subject distance: 2 m;
- Bowens softbox installed;
- portrait/half-body framing;
- useful key-light exposure remains achievable.

### V3 — Fill light

- 1–2 m working distance;
- dimmed output;
- smooth low-level control without objectionable color or temporal instability.

### V4 — Normal-speed video

- cameras and smartphones;
- 24/25/30/50/60 fps as applicable;
- common cinematic and faster shutter settings;
- no visible temporal artefacts within the validated matrix.

### V5 — Sustained operation

- continuous output at a defined high-load operating point;
- thermal equilibrium reached;
- no unsafe temperature, uncontrolled output drift, or unacceptable acoustic behavior.

## 13. Open engineering questions

The next work should resolve these questions in roughly this order:

1. What reference exposure scenarios should define required illuminance through the softbox at 1 m and 2 m?
2. Is 2700–6500 K the best useful range after emitter-efficiency and color-quality trade-offs are considered?
3. What emitter/channel architecture provides high-quality tunable white while preserving a path to tint/spectral expansion?
4. What temporal-modulation strategy is required for the camera/shutter matrix?
5. What electrical power follows from those optical requirements?
6. What external DC voltage and connector are appropriate at that power level?
7. What thermal architecture and acoustic target follow from sustained power dissipation?
8. Which local parameters actually exist in Phase 1, and does the encoder require supporting buttons?

These questions must be answered before individual LED, driver, connector, fan,
or MCU part numbers are frozen.
