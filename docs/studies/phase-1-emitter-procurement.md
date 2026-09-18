# Phase 1 Emitter Procurement Study

## 1. Purpose

This study tracks whether the high-power tunable-white emitter candidates are
actually procurable before OpenSpectralLight commits electrical, thermal, or
mechanical design work around them.

The current primary research candidates are the Yujileds tunable-white matrix
families marketed as 300W-720W and 500W-1440W. These are not approved BOM parts.

## 2. Public availability status

As checked on 2026-09-18:

- Yujileds still lists both tunable-white matrix families in its current product
  catalog and explicitly targets photographic, film, and studio lighting.
- The 300W-720W and 500W-1440W matrix products are not exposed with a public
  price in the Yujileds retail store found during this study.
- Yujileds provides direct US and China sales contacts and an enquiry form.
- Procurement must therefore be treated as **quote/sample-request based** until
  Yujileds confirms otherwise.

The absence of a public store listing must not be interpreted as
discontinuation. The products remain present on Yujileds' current product pages.

## 3. Product-code inconsistency

There is an important documentation mismatch:

| Source | 300 W-class code | 500 W-class code |
|---|---|---|
| Current Yujileds product pages | `B3220003.26` | `B3220005.26` |
| LED Matrix Solution datasheet V1.5 linked from those pages | `B3240003.26` | `B3240005.26` |

The electrical/optical values shown on the current product pages match the V1.5
datasheet values, but the part-number prefix differs.

**Procurement gate:** do not issue a purchase order against either code until
Yujileds confirms the currently orderable part number and whether the other code
is obsolete, superseded, or a documentation error.

## 4. Information required from Yujileds

The first manufacturer enquiry should request all of the following for both the
300 W-class and 500 W-class tunable-white matrices:

1. Current orderable part number and current datasheet revision.
2. Sample quantity availability and unit sample price.
3. MOQ and volume-price breaks relevant to low-volume open-source prototyping.
4. Lead time and shipping options to Ecuador.
5. Whether standard chromaticity bins can be specified at order time.
6. Recommended mounting method, thermal-interface material, flatness/contact
   requirements, and mounting force/torque.
7. Maximum recommended continuous substrate/case temperature and any
   current-versus-temperature derating curve.
8. Safe **continuous simultaneous WW + CW current/power envelope**. This must
   include whether each channel may run at its published rated test current
   simultaneously and any total-package power limit.
9. Recommended maximum current for long-life continuous studio use, distinct
   from absolute maximum ratings.
10. Luminous-flux and chromaticity behavior versus current and temperature.
11. Machine-readable SPD data for 2700 K and 6500 K endpoints and, if available,
    representative mixed-CCT operating points.
12. Available reliability/lifetime data, including LM-80-style data or the
    nearest equivalent evidence for this matrix construction.
13. Confirmation that the published R9, TM-30, TLCI, and SSI values apply to the
    currently supplied revision.

## 5. Procurement decision states

The candidate may progress through these states:

`DISCOVERED -> QUOTED -> SAMPLE_AVAILABLE -> ELECTRICALLY_CLARIFIED -> BENCH_VALIDATED -> BOM_CANDIDATE`

A product-page listing alone is only `DISCOVERED`.

No matrix becomes a `BOM_CANDIDATE` until its continuous mixed-channel limits,
procurement path, and bench behavior are known.

## 6. Current assessment

The Yujileds matrices remain the preferred high-power prototype direction
because their compact mixed emitter geometry is well matched to a Bowens head.
However, procurement risk is materially higher than for mainstream general-
lighting COBs because price, MOQ, lead time, and combined-channel operating
limits are not public.

Bridgelux Vesta Thrive remains the fallback/reference path because its
documentation and package limits are clearer, even though reaching the current
upper-output target with many small COBs is mechanically unattractive.

## References

1. Yujileds, **Tunable White (300W-720W)**.
   https://www.yujiintl.com/tunable-white-300w-720w/
2. Yujileds, **Tunable White (500W-1440W)**.
   https://www.yujiintl.com/tunable-white-500w-1440w/
3. Yujileds, **LED Matrix Solution Introduction & Datasheet, V1.5**.
   https://www.yujiintl.com/wp-content/uploads/2022/09/Yujileds-LED-Matrix-Solution-V1.5.pdf
4. Yujileds, **Contact**.
   https://www.yujiintl.com/contact.html
5. Yujileds, **Retail store**.
   https://store.yujiintl.com/
