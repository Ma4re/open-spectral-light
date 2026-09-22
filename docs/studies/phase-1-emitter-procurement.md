# Phase 1 Emitter Procurement Study

## 1. Purpose

This study evaluates emitter candidates not only by optical performance but by
whether OpenSpectralLight can realistically source, document, replace, and
maintain them.

For the base light engine, procurement is an engineering requirement rather than
an afterthought.

## 2. Supply-chain rule

The primary Phase 1 emitter path shall not depend on a manufacturer answering a
sales or engineering enquiry before prototype quantities can be purchased.

A preferred emitter family should provide:

- public datasheets and lifecycle status;
- active orderable part numbers;
- prototype quantities through an authorized distributor;
- transparent pricing or normal distributor quotation flow;
- published electrical, thermal, and optical limits;
- a credible replacement or second-source strategy at the light-engine-module
  level.

Direct manufacturer contact may still be useful for application support, but it
must not be a prerequisite for buying the base emitter.

## 3. Yujileds status

The Yujileds 300 W- and 500 W-class bicolor matrices remain technically
interesting, but they are **removed from the primary Phase 1 path**.

Reasons:

- public pricing and normal prototype ordering are not exposed;
- the public product pages and linked V1.5 datasheet disagree on the B322/B324
  part-number prefix;
- the combined continuous WW + CW electrical envelope is not stated clearly
  enough for driver design;
- a direct technical/sample enquiry sent by the project did not receive a
  response.

This is a procurement decision, not a claim that the Yujileds optical design is
poor.

Yujileds may remain an external benchmark, but OpenSpectralLight shall not base
its first hardware revision on it.

## 4. Distributor-backed candidate families

### 4.1 Bridgelux Vesta tunable-white COBs

Bridgelux lists Arrow, Digi-Key, Farnell, Newark, and Future Lighting Solutions
among its official distribution channels.

Current examples found through authorized distribution include active Vesta
2700–6500 K tunable-white COBs. The 18 mm
`BXRV-TR-2765G-40A0-A-23` is active and was available from Digi-Key in
prototype quantities during this review.

Advantages:

- integrated warm/cool mixing within each COB;
- simple two-channel electrical model;
- public datasheets;
- normal distributor procurement;
- multiple official distributors.

Limitation:

- the readily stocked high-output variant is around CRI 92, below the preferred
  high-fidelity direction.

This family is a strong **mechanical/electrical prototype and fallback** option.

### 4.2 Bridgelux Thrive single-CCT COBs

Bridgelux Thrive provides higher-fidelity white points. Active V18 Thrive
single-CCT COBs are available in both warm and cool white.

During this review Digi-Key listed, among others:

- `BXRE-27S4001-C-73`, 2700 K, active, prototype-quantity stock;
- `BXRE-65S4001-C-74`, 6500 K, active, prototype-quantity stock.

Both use the same approximately 24 mm package / 19.2 mm LES class and are
nominally around 34 V at the published test point.

Advantages:

- high-fidelity white family;
- standard active COBs rather than a custom film-light matrix;
- warm and cool endpoints can be selected independently;
- distributor-backed procurement;
- scalable by using multiple identical COBs.

Limitations:

- warm and cool light originate from separate physical COBs;
- the light engine therefore needs deliberate interleaving/mixing;
- a high-power implementation uses multiple COBs and a larger effective source
  area.

This is now the preferred **high-fidelity architecture research direction**.

### 4.3 Luminus Dynamic COB

Luminus officially supports Digi-Key, Mouser, Future Electronics, and Avnet.

Its CCT-tunable Dynamic COB family is electrically attractive and has a compact
integrated warm/cool geometry. However, the older TW01 parts are being
discontinued and current replacement availability must be checked by exact part
number before use.

Its CRI level also remains weaker than the preferred photography/video quality
target.

Luminus remains a secondary/fallback family rather than the current leading
candidate.

### 4.4 Citizen tunable-white COB

Citizen currently publishes CLUD22 and CLUD32 tunable-white COB families with
2700 K and 6500 K endpoints and provides current optical/mechanical files.

The family is technically relevant, but the project has not yet confirmed
prototype stock through the preferred distributors for the exact CLUD parts.

Citizen remains on the watchlist pending distributor verification.

## 5. Preferred sourcing architecture

The preferred Phase 1 sourcing strategy is now:

1. use standard, active, distributor-backed emitter parts;
2. make the **light-engine carrier/mixing assembly our replaceable module**;
3. keep the driver/controller boundary independent of one exact emitter family
   where practical;
4. validate at least one alternative emitter family before freezing production
   hardware;
5. avoid direct-vendor-only custom matrices for the base configuration.

This intentionally trades some optical/mechanical simplicity for supply-chain
control and long-term maintainability.

## 6. Current prototype direction

The first emitter prototype should compare two distributor-backed approaches:

### Prototype A — high-fidelity split-white engine

- multiple Bridgelux Thrive single-CCT COBs;
- 2700 K and 6500 K COBs arranged symmetrically/interleaved;
- WW and CW banks driven independently;
- common heat spreader;
- optical mixing stage before the Bowens modifier interface.

Purpose: determine whether high color quality and acceptable spatial/angular
mixing can be achieved with standard parts.

### Prototype B — integrated tunable-white reference

- multiple standard Bridgelux Vesta 2700–6500 K tunable-white COBs;
- integrated WW/CW mixing inside each COB;
- same approximate optical/power target as Prototype A.

Purpose: establish the mixing advantage and quantify the color-quality trade-off
of a simpler integrated tunable-white engine.

The comparison should decide whether the final Phase 1 engine prioritizes the
higher-fidelity split-white architecture or the simpler integrated Vesta
architecture.

## 7. Procurement decision states

Candidates may progress through:

`DISCOVERED -> DISTRIBUTOR_VERIFIED -> SAMPLE_PURCHASABLE -> BENCH_VALIDATED -> BOM_CANDIDATE`

For the primary light engine, `SAMPLE_PURCHASABLE` means the project can order
prototype quantities without waiting for manufacturer sales approval.

## 8. Decision gate

No emitter family becomes a BOM candidate until:

- current lifecycle status is active;
- exact prototype quantities can be ordered;
- distributor and lead-time risk are acceptable;
- optical/color data meet the project direction;
- thermal/electrical limits are public and usable;
- the light-engine module has a realistic substitution strategy.

## References

1. Bridgelux, **Where to Buy**.
   https://www.bridgelux.com/where-buy
2. Bridgelux, **Vesta Series**.
   https://www.bridgelux.com/vesta-series
3. Bridgelux, **Thrive**.
   https://www.bridgelux.com/thrive
4. Digi-Key, **BXRV-TR-2765G-40A0-A-23**.
   https://www.digikey.com/en/products/detail/bridgelux/BXRV-TR-2765G-40A0-A-23/10279860
5. Digi-Key, **BXRE-27S4001-C-73**.
   https://www.digikey.com/en/products/detail/bridgelux/BXRE-27S4001-C-73/11203640
6. Digi-Key, **BXRE-65S4001-C-74**.
   https://www.digikey.com/en/products/detail/bridgelux/BXRE-65S4001-C-74/11203636
7. Luminus, **Where to Buy**.
   https://www.luminus.com/contact/wheretobuy
8. Luminus, **Dynamic COB Modules — CCT Tunable**.
   https://www.luminus.com/products/dynamic-cob/cct-tunable
9. Citizen, **CITILED Tunable White COB**.
   https://ce.citizen.co.jp/productscn/led_category/detail/Tunable_White_COB
