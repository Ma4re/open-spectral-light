# Phase 1 Light-Engine Architecture Study

## 1. Purpose

This study compares light-engine directions for the Phase 1 tunable-white
fixture. It does not freeze an emitter or driver.

The governing product target is currently:

- continuous key/fill light for portrait and music-video work;
- Bowens S-mount;
- approximately 1–2 m working distance;
- target CCT range around 2700–6500 K;
- high-quality skin rendering;
- manual local control in Phase 1;
- architecture prepared for later tint/Duv and spectral-control capability;
- upper engineering target of 1200 lx at 2 m through the defined large
  modifier case.

## 2. The upper-output target is a major architecture driver

The current 1200 lx limit case is intentionally demanding: a nominal 120 cm
Bowens octabox/parabolic modifier, substantial diffusion, grid, and a 2 m
subject distance.

Commercial photometric data provide an order-of-magnitude check. Aputure's
Light OctaDome 120 publishes these measurements with its 2.5-stop front
diffusion:

| Fixture | Fixture power class | 1 m | 3 m |
|---|---:|---:|---:|
| LS 300x | <=350 W input | 2335 lx | 347 lx |
| LS 600x Pro at 5600 K | <=600 W light output / <=720 W input | 5230 lx | 825 lx |

The published OctaDome data do not state these numbers with the fabric grid
installed, and they do not include a 2 m point. They therefore cannot be used to
derive an exact OpenSpectralLight wattage.

They are sufficient to show that the present upper target belongs to a
**several-hundred-watt fixture class**, not a conventional 60–100 W COB class.
The target remains valid as a TARGET, but electrical power, thermal mass, fan
noise, connector current, and cost must be checked before it is frozen.

## 3. Architectural options

### A. One integrated tunable-white COB

A single package contains warm and cool white channels behind one LES.

**Strengths**

- best point-source-like geometry;
- easy Bowens reflector/softbox coupling;
- simple two-channel electrical interface;
- warm/cool dies are already spatially interleaved by the package manufacturer;
- simplest calibration and assembly.

**Weaknesses**

- mainstream general-lighting high-fidelity COBs are below the optical power
  implied by the current limit case, although specialized film/studio matrices
  now exist at much higher power;
- two channels provide limited independent Duv/tint control;
- the complete base design depends strongly on one package family;
- adding correction channels around the COB creates a second emitting geometry.

**Conclusion:** excellent architecture for a lower-power fixture, but one
mainstream high-quality tunable-white COB is unlikely to satisfy the current
upper-output target by itself.

### B. Cluster of integrated tunable-white COBs

Several identical tunable-white COBs are placed symmetrically around the optical
axis and driven as matched warm/cool channel groups.

**Strengths**

- scalable power using documented standard parts;
- preserves a simple two-command-channel base model;
- each COB already mixes warm/cool dies locally;
- easier to prototype and replace than a custom high-power LED board;
- suitable for a softbox-first product where perfect point-source focusing is
  not the primary goal.

**Weaknesses**

- larger effective emitting area;
- harder coupling to narrow reflectors or Fresnels;
- thermal spreading and current balancing become important;
- multiple COB tolerances must be characterized;
- correction-channel placement must avoid spatial color separation.

**Conclusion:** useful fallback and quality-reference architecture, but no
longer the preferred first high-power prototype because purpose-built bicolor
film/studio matrices can provide a much more compact emitting geometry.

### C. Separate warm and cool high-fidelity COBs

One or more warm-white COBs and separate cool-white COBs are mixed optically.

**Strengths**

- broad choice of high-fidelity single-CCT LED families;
- power can be scaled independently;
- technologies such as Nichia Optisolis can provide very high spectral fidelity.

**Weaknesses**

- warm and cool channels originate at different physical positions;
- color uniformity changes with angle and distance unless a good mixing stage is
  used;
- Bowens point-source optics become more difficult;
- two thermally different emitter sets can drift differently.

**Conclusion:** useful research/prototype option, but less attractive than
locally mixed tunable-white COBs for a compact Bowens head unless its spectral
advantage proves substantial.

### D. Custom interleaved multi-channel LED array

Warm white, cool white, and optional correction channels are interleaved on a
custom metal-core or ceramic-backed light-engine PCB.

**Strengths**

- channel count and spectral endpoints are under project control;
- auxiliary channels can be placed uniformly rather than bolted on later;
- scalable electrical power;
- potentially the cleanest long-term route to tint/Duv and richer spectral
  control.

**Weaknesses**

- highest hardware and manufacturing complexity;
- many emitters and current paths;
- custom optical/thermal validation required;
- achieving a compact high-luminance source is harder than using a COB;
- component substitution can affect calibration and board layout.

**Conclusion:** best long-term premium architecture candidate, but premature as
the first hardware implementation.

### E. Base tunable-white engine plus auxiliary correction channels

A high-output two-white engine provides almost all luminous flux. Lower-power
auxiliary channels add tint/Duv or spectral correction.

**Strengths**

- premium functionality need not carry the full key-light optical power;
- base light remains useful without optional sensing or correction;
- correction channels can be sized for chromaticity authority rather than total
  illuminance;
- preserves a clean product distinction between base quality and advanced
  spectral control.

**Weaknesses**

- correction channels still need excellent spatial mixing with the white engine;
- their required wavelengths and power cannot be selected before spectral
  measurements/modeling;
- a correction ring placed far from the white LES can create angular color
  nonuniformity.

**Conclusion:** preferred capability model for the project, independent of
whether the base white engine is a COB cluster or a future custom array.

## 4. Current primary-source candidate families

The parts below are **research candidates**, not approved BOM items.

| Family | Relevant published data | Why it matters | Current limitation |
|---|---|---|---|
| Bridgelux Vesta Thrive tunable white | 2700–6500 K variants; typical CRI 98; 18 mm array about 32 W nominal per endpoint at nominal current | Strong documented color quality and integrated warm/cool mixing | One unit is far below the current upper-output requirement; a large cluster would increase emitting area |
| Yujileds 300 W-class bicolor matrix | 2700/6500 K; 4 A endpoint test; 10.8/13.8 klm; Ra >=95, R9 90, TM-30 Rf/Rg 92/100, TLCI 97; ~40 mm emitting region | Purpose-built high-power film/studio source in compact geometry | Combined continuous WW/CW power envelope and procurement must be confirmed |
| Yujileds 500 W-class bicolor matrix | 2700/6500 K; 8.4 A endpoint test; 20.0/25.6 klm; Ra >=95, R9 90, TM-30 Rf/Rg 92/100, TLCI 97 | Single-matrix candidate closest to the current high-output limit case | High thermal load; combined continuous WW/CW power envelope and procurement must be confirmed |
| Bridgelux Vesta standard / SE high-output TW | 2700–6500 K; up to roughly 55 W nominal and 7.35 klm in current SE 29 mm family; CRI around 92 | Higher flux in an established tunable COB ecosystem | Color-quality target is weaker than Thrive |
| Luminus Dynamic COB Gen 2 CTM-22 | 2700 K / 6500 K; CRI min 90; 36 W nominal per channel; about 6010/6480 lm at Tj=85 C at the endpoints | High output from a compact dual-channel COB | CRI 90 alone is not enough evidence for the desired camera/skin quality |
| Nichia tunable-white COB | Current recommended/legacy tunable-white families exist around 10–42 W class | Strong manufacturer documentation and binning | Published tunable-white examples are CRI 90 / R9 50 class, below the desired fidelity direction |
| Nichia Optisolis single-CCT COB/SMD | Ra98–99 technology positioning; high-fidelity warm and cool products | Useful spectral-quality benchmark and possible separate-white/custom-array source | Not a high-power integrated 2700–6500 K tunable COB; would require multi-emitter mixing |

No family is selected yet. The Yujileds product pages and linked V1.5 datasheet
currently disagree on the B322/B324 part-number prefix for both high-power
bicolor candidates. Procurement, pricing, thermal data, guaranteed bins, SPD
access, and the valid order code must be confirmed before a BOM decision.

Procurement status is tracked in
[`phase-1-emitter-procurement.md`](phase-1-emitter-procurement.md).

## 5. Color-quality observation

A two-white engine remains attractive for Phase 1 because it minimizes
complexity, but it must not be evaluated only with CRI Ra.

For a photography/video source the next comparison should prioritize:

- endpoint and intermediate SPDs;
- R9 and TM-30 Rf/Rg where available;
- Duv versus CCT across the full mixing trajectory;
- consistency versus junction/case temperature;
- consistency versus drive current;
- camera tests on skin tones and saturated materials.

Bridgelux Thrive is notable in the present survey because the manufacturer
publishes 98 CRI, R1–R15 greater than 90, and TM-30 Rf 96 / Rg 99 for the
technology. This makes it a useful **quality reference**, even if its available
power per COB may require clustering.

## 6. Recommended architecture direction for the first prototype

The first prototype should **not** begin with a custom multi-channel MCPCB or a
large many-COB cluster.

The current preferred experiment is a **purpose-built high-power bicolor
film/studio matrix**, with the Yujileds 300 W- and 500 W-class modules as primary
research candidates. Bridgelux Thrive remains the quality/fallback reference.

The detailed power comparison and the unresolved combined-channel limits are
documented in
[`phase-1-power-envelope.md`](phase-1-power-envelope.md).

This is a prototype direction, not an ADR. The high-power matrix must still pass
procurement, optical, spectral, thermal, and acoustic validation before any part
number is frozen.

## 7. What should be reserved for premium capability

The base engine should provide:

- high-quality tunable white;
- stable intensity control;
- enough optical output for the accepted key/fill use cases;
- local CCT/intensity operation without optional modules.

The architecture should be capable of later adding:

- one or more auxiliary spectral correction channels;
- measured Duv/tint control;
- spectral sensing;
- closed-loop calibration/compensation;
- richer remote control.

The premium path should therefore be enabled by **channel and interface
headroom**, not by intentionally weakening the base light.

## 8. Immediate experimental questions

Before selecting a PCB or power connector, the next emitter work should answer:

1. Can the 300 W-class high-power matrix satisfy the normal 90 cm modifier
   scenario with useful margin?
2. Is the 500 W-class matrix actually required to approach 1200 lx at 2 m with
   the 120 cm limit modifier and grid?
3. What is the real output penalty of the selected diffusion layers and grid?
4. How much does output change across 2700, ~4300, and 6500 K?
5. What Duv path results from warm/cool mixing?
6. How much color/output drift occurs from cold start to thermal equilibrium?
7. What safe continuous combined WW/CW current envelope does the matrix
   manufacturer authorize?
8. What total electrical power, heatsink dissipation, and fan airflow result?
9. What auxiliary-channel authority would be needed for useful tint/Duv
   correction?

Those measurements determine the driver and thermal architecture more reliably
than nominal LED wattage.

## References

1. Aputure, **Light OctaDome 120**, photometrics and modifier data.
   https://aputure.com/en-US/products/light-octadome-120
2. Aputure, **LS 600x Pro**, power and photometric specifications.
   https://aputure.com/en-US/products/ls-600x-pro
3. Bridgelux, **Vesta Thrive**.
   https://www.bridgelux.com/vesta-thrive
4. Bridgelux, **Vesta Series**.
   https://www.bridgelux.com/vesta-series
5. Luminus, **Dynamic COB Modules — CCT Tunable**.
   https://www.luminus.com/products/dynamic-cob/cct-tunable
6. Nichia, **COB Lighting**.
   https://led-ld.nichia.co.jp/en/product/lighting_cob.html
7. Nichia, **Optisolis Lighting**.
   https://led-ld.nichia.co.jp/en/product/lighting_optisolis.html
8. U.S. Department of Energy, **Understanding LED Color-Tunable Products**.
   https://www.energy.gov/cmei/ssl/understanding-led-color-tunable-products
9. Yujileds, **LED Matrix Solution Introduction & Datasheet, V1.5**.
   https://www.yujiintl.com/wp-content/uploads/2022/09/Yujileds-LED-Matrix-Solution-V1.5.pdf
