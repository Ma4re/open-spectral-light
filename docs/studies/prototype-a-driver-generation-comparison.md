# Prototype A — LED Driver Generation Comparison

## 1. Purpose

Prototype A originally used TI LM3409HV as the baseline branch controller because
it matches the electrical problem unusually well: a 48 V bus, a roughly 31–42 V
LED load, and branch current above 2 A.

LM3409HV remains active and technically valid, but it is an older controller
architecture. This study asks whether a newer device materially improves the
Prototype A power stage rather than replacing it merely because of age.

The comparison uses the same branch target for every candidate:

| Parameter | Prototype A branch |
|---|---:|
| Nominal input | 48 V |
| Input class | regulated external DC |
| Typical LED voltage | ~35.5 V |
| LED design envelope | ~31–42 V |
| Normal maximum LED current | ~2.11 A |
| Approximate output power | ~75 W |
| Branch count in full PSM | 8 |

## 2. Decision criteria

A newer driver is better only if it improves the product-relevant trade-off:

1. electrical efficiency and branch thermal load;
2. camera-oriented dimming behavior;
3. EMI predictability with eight converters;
4. fault detection and safe shutdown;
5. component count and PCB area;
6. current headroom above 2.11 A;
7. voltage/transient margin around a 48 V bus;
8. simulation and evaluation support;
9. distributor availability and lifecycle;
10. implementation complexity.

## 3. LM3409HV — legacy benchmark that remains valid

LM3409HV is still an active 75 V PFET buck-current controller.

Relevant strengths:

- 6–75 V input range;
- external PFET allows power-device optimization;
- up to 5 A LED-current class;
- differential high-side current sense;
- 250:1 analog dimming;
- 10,000:1 PWM dimming;
- cycle-by-cycle current limiting;
- no conventional control-loop compensation;
- large voltage margin above a nominal 48 V bus.

Its evaluation board is especially relevant because it demonstrates:

- 48 V input;
- 42 V LED output;
- 1.5 A;
- ~400 kHz target switching frequency;
- 97 % design-efficiency assumption.

The weakness is not that it is obsolete; it is not. The weaknesses for this
specific product are:

- external PFET and gate-drive optimization;
- external Schottky freewheel diode;
- constant-off-time switching frequencies are not naturally synchronized across
  eight branches;
- less integrated fault diagnosis;
- no integrated thermal-foldback policy;
- older simulation/reference ecosystem.

LM3409HV remains a strong fallback and comparison branch.

## 4. TPS92205x — current preferred family

The TPS92205x family is a newer TI illumination LED-driver family with 2 A and
4 A variants.

For the 4 A variants relevant to Prototype A, TPS922054 and TPS922055 provide:

- 4.5–65 V input range;
- integrated 150 mOhm switching NMOS;
- 4 A output-current class;
- 100 kHz to 2.2 MHz configurable switching frequency;
- 256:1 analog dimming;
- fast PWM dimming;
- hybrid and flexible dimming modes;
- LED open/short detection;
- switching-FET open/short protection;
- sense-resistor fault protection;
- cycle-by-cycle current limiting;
- thermal shutdown;
- configurable thermal foldback;
- open-drain FAULT output;
- current PSpice/SIMPLIS support.

This family removes the external main switching MOSFET and its gate-drive design
from each branch.

### 4.1 Reference design is unusually close to Prototype A

TI's datasheet includes a design example with:

- 48 V input rail with +/-10 % variation;
- 36 V LED output;
- 2 A maximum LED current;
- 1.2 MHz switching frequency;
- 10 uH inductor;
- <40 % specified inductor-current-ripple criterion.

That is close enough to Prototype A that the device has much lower architecture
risk than a generic LED-driver candidate.

The same datasheet publishes typical efficiency curves at 48 V input; long LED
strings near the Prototype A voltage range operate in the mid-to-high-90-percent
region in the typical characterization. Final Prototype A efficiency must still
be measured at its actual 2.11 A, frequency, inductor, diode, layout, and
temperature.

### 4.2 Full-scale current sensing

TPS92205x uses a 200 mV full-scale sense threshold.

For Prototype A:

```text
RSENSE = 0.200 V / 2.11 A
       ~= 94.8 mOhm
```

The nominal DC sense-resistor loss is:

```text
P ~= I^2 R
  ~= 0.422 W
```

A >=1 W low-TCR Kelvin-sensed resistor remains appropriate for prototype work.

### 4.3 Integrated MOSFET loss

The published integrated switch resistance is approximately 150 mOhm.

At:

```text
I = 2.11 A
D ~= 35.5 / 48 ~= 0.74
```

a first-order room-temperature conduction estimate is:

```text
P_FET ~= I^2 R D
      ~= 0.49 W
```

This is higher than the conduction loss obtainable from a carefully selected
external low-RDS(on) PFET in the LM3409HV architecture.

Therefore TPS92205x is **not selected because an integrated switch guarantees
higher efficiency**.

Its benefit is the system trade:

- smaller BOM;
- smaller branch area;
- less gate-drive design;
- integrated diagnostics;
- integrated thermal functions;
- modern dimming behavior;
- modern simulation support.

Actual total branch efficiency decides the thermal comparison.

## 5. TPS922054 versus TPS922055

The two 4 A family members should be treated as an intentional A/B option.

### TPS922054

Spread spectrum disabled.

TI explicitly states that the non-spread-spectrum variants are intended to
provide better brightness performance in low-brightness scenarios.

This makes TPS922054 the preferred **first optical-characterization device**.

### TPS922055

Spread spectrum enabled:

- approximately +/-7 % switching-frequency spreading;
- approximately 2 kHz modulation frequency.

Its purpose is to reduce energy concentrated at the switching fundamental and
harmonics, improving EMI behavior and potentially reducing filtering burden.

For an eight-converter camera light, this is attractive, but the 2 kHz
frequency modulation itself must not simply be assumed optically irrelevant.

### Recommended PCB strategy

Use the same selected package/footprint and design the branch so the
corresponding TPS922054 and TPS922055 package variants can be A/B tested without
changing the PSM architecture.

The validation question is:

> Does spread spectrum materially improve eight-branch EMI without introducing
> measurable low-frequency optical artifacts or degrading deep-dimming quality?

Do not answer that question from the datasheet alone.

## 6. Dimming behavior

TPS92205x provides three especially relevant modes.

### Analog dimming

The external ADIM/HD pin receives a duty-coded command, but the device uses that
command to change the internal current reference. The LEDs therefore remain
continuously current-regulated rather than being externally time-division
switched in normal analog mode.

This is well aligned with the camera-light architecture.

The family advertises 256:1 analog dimming, but low-end accuracy is not uniform
across the entire headline ratio. Datasheet limits around ~1.17 % of full scale
are much looser than around 12.5 %.

Therefore the fixture still needs optical/current characterization at low output.

### Hybrid dimming

The built-in hybrid mode uses analog current control from approximately 12.5 %
to 100 %, and switches to internal PWM below approximately 12.5 %.

This is convenient but does not automatically become the OpenSpectralLight
policy because the camera requirements may prefer a different crossover.

### Flexible dimming

Flexible mode separates:

- analog-current command; and
- LED on/off PWM command.

This gives OpenSpectralLight the most control and best matches the existing
architecture:

- normal video range uses current control;
- deep dimming may add a common synchronized temporal gate only if measured
  behavior requires it.

Flexible mode is therefore the most interesting production-control direction.

## 7. Switching-frequency and inductor direction

The TI reference design uses 1.2 MHz and 10 uH, but TI also explicitly notes that
lower switching frequency generally improves efficiency and thermal behavior.

Prototype A does not need to minimize the inductor at all costs.

A sensible first comparison range is **400–600 kHz**.

Using the standard buck-ripple equation near the high-input design corner gives
useful first-order combinations such as:

| Frequency | Inductance | Approx. ripple near Prototype A point |
|---:|---:|---:|
| 400 kHz | 68 uH | ~0.43 A p-p (~20 %) |
| 600 kHz | 47 uH | ~0.41 A p-p (~19 %) |
| 600 kHz | 56 uH | ~0.34 A p-p (~16 %) |

These are branch-study starting points, not frozen parts.

The first prototype should compare thermal/efficiency/EMI rather than maximizing
switching frequency for PCB compactness.

## 8. Output capacitor

Unlike the original LM3409HV capacitor-less baseline, the TPS92205x reference
design intentionally uses output capacitance.

This is attractive for OpenSpectralLight because reducing LED-current ripple
directly may reduce optical switching ripple.

Prototype A should follow TI's general architecture and reserve:

- local output capacitance;
- test points for LED current and optical detector measurements;
- the ability to vary COUT during characterization.

Exact capacitance remains part of the TPS92205x branch design.

## 9. Voltage margin

This is the clearest architectural advantage LM3409HV retains.

LM3409HV:

```text
VIN absolute class: 75 V
```

TPS92205x:

```text
VIN absolute class: 65 V
recommended operating ceiling below that
```

A regulated 48 V external source still fits comfortably, but input protection
now matters more.

If TPS92205x becomes the production baseline, the head input contract must make
sure that:

- PSU tolerance;
- connector/cable transients;
- hot-plug/inrush behavior;
- TVS clamping;
- external battery adapter behavior;

cannot expose the driver to an unsafe voltage.

This is manageable for a regulated studio-light bus and does not justify
rejecting the family, but it must be validated before 48 V becomes FROZEN.

## 10. TPS925302-Q1 comparison

TPS925302-Q1 is a very recent dual-channel synchronous buck LED driver with:

- 4.5–65 V input;
- two channels;
- up to 2 A per channel;
- synchronous conversion;
- SPI;
- diagnostic/fault features;
- switching up to 1.2 MHz.

It is architecturally attractive because four ICs could provide eight branches.

However:

- 2.0 A maximum is below Prototype A's ~2.11 A branch target;
- it leaves no useful current headroom;
- SPI and a 48-pin automotive-oriented device add significant complexity;
- the stated useful analog-control range is not compelling enough to offset that
  complexity for this product.

It remains a technology reference, not the preferred Rev A candidate.

## 11. LT8376 comparison

Analog Devices LT8376 is recommended for new designs and provides:

- 3.6–60 V input;
- 3 A integrated synchronous switches;
- Silent Switcher architecture;
- 200 kHz–2 MHz operation with synchronization;
- spread-spectrum frequency modulation;
- +/-1.5 % LED-current regulation;
- 20:1 analog LED-current control;
- 5000:1 PWM dimming at 100 Hz;
- open/short LED protection and fault indication.

Its synchronous topology and EMI design are attractive.

For Prototype A, however:

- 60 V maximum input margin is even tighter than TPS92205x;
- only 20:1 analog current control moves the system toward PWM earlier;
- the product's camera-oriented continuous-current requirement makes the deeper
  TPS92205x analog/flexible dimming behavior more valuable.

LT8376 remains the strongest non-TI alternative in the current shortlist.

## 12. Decision matrix

| Criterion | LM3409HV | TPS922054/055 | TPS925302-Q1 | LT8376 |
|---|---|---|---|---|
| Meets 2.11 A with margin | Yes | **Yes** | No / marginal | Yes |
| Input-voltage margin | **Best** | Good | Good | Lowest |
| Integrated main switch | No | **Yes** | Yes | Yes |
| Synchronous | No | No | **Yes** | **Yes** |
| Wide current dimming | Very good | **Very good** | Moderate | Limited |
| Camera-oriented flexible dimming | Basic | **Best** | Good | PWM-oriented |
| Fault diagnostics | Basic | **Strong** | Strong | Strong |
| Thermal foldback | External | **Integrated/configurable** | Integrated | External/system |
| EMI tools | Variable COFT | **Optional SS, configurable fSW** | Fixed/sync family | **Silent Switcher/sync/SS** |
| Design complexity | Medium | **Low–medium** | High | Medium |
| Current reference design match | Excellent | **Excellent** | Moderate | Moderate |
| Voltage transient robustness | **Best** | Good with input protection | Good | More constrained |
| Current recommendation | Fallback/benchmark | **Preferred baseline** | Not preferred | Alternate |

## 13. Recommendation

Prototype A should move its branch-development baseline from **LM3409HV** to the
**TPS92205x 4 A family**.

More specifically:

1. design the first branch around the TPS922054/055 shared electrical family;
2. populate TPS922054 first for low-brightness optical characterization;
3. populate TPS922055 on otherwise equivalent hardware for EMI comparison;
4. keep the LM3409HV branch study as a fully viable fallback and performance
   benchmark;
5. do not freeze either TPS variant until optical, thermal, EMI, and supply-chain
   tests are complete.

This is a change in **implementation baseline**, not a change to the PSM/LEM
architecture.

The system remains:

```text
48 V candidate bus
      |
8 independent constant-current buck branches
      |
4 WW + 4 CW LEM branches
```

## 14. First TPS92205x branch target

The first detailed branch design should use:

| Parameter | Initial target |
|---|---|
| Family | TPS922054 / TPS922055, 4 A variant |
| Vin | 48 V nominal |
| Input design range | derive from final 48 V source tolerance |
| VLED | ~31–42 V |
| ILED full scale | ~2.11 A |
| RSENSE | ~94.8 mOhm, >=1 W, low TCR |
| Switching frequency | **400 kHz baseline**; 600 kHz A/B only |
| Inductor | **47 uH @400 kHz baseline**; 33 uH @600 kHz A/B |
| Dimming baseline | analog/flexible current control |
| Deep dimming | common synchronized PWM only if validated |
| Output capacitor | populated/characterized |
| Spread spectrum | A/B test TPS922054 vs TPS922055 |
| Fault | expose FAULT to PSM/controller telemetry |
| Thermal | characterize integrated-FET junction/board temperature |

## 15. Next exact step

Detailed analytical branch sizing is now complete and documented in
[`prototype-a-tps92205x-branch.md`](prototype-a-tps92205x-branch.md).

The preferred electrical baseline is TPS922054 in VSON DMT, 400 kHz, 47 uH,
~95 mOhm current sense, and ~3–5 uF effective output capacitance. TPS922055 and
600 kHz / 33 uH remain deliberate A/B variants.

The next step is current TI PSpice/SIMPLIS simulation and a one-branch PCB. The
design must verify:

- 31 V, 35.5 V, and ~41.2 V LED points;
- 2.11 A full-scale accuracy;
- 48 V source tolerance and dropout margin;
- 400 vs 600 kHz efficiency/thermal trade;
- inductor and output-capacitor selection;
- analog-dimming accuracy down to the useful continuous-current limit;
- flexible/hybrid mode temporal behavior;
- startup/shutdown and fault recovery;
- TPS922054 versus TPS922055 optical waveform and EMI behavior;
- 65 V device-limit margin through the common input-protection stage.

## References

1. Texas Instruments, **TPS92205x 65V 2A / 4A Buck LED Driver with Inductive
   Fast Dimming**, Rev. B, 2025.
   https://www.ti.com/lit/ds/symlink/tps922052.pdf
2. Texas Instruments, **TPS922054 product page**.
   https://www.ti.com/product/TPS922054
3. Texas Instruments, **TPS922055 product page**.
   https://www.ti.com/product/TPS922055
4. Texas Instruments, **LM3409HV product page and evaluation board**.
   https://www.ti.com/product/LM3409HV
5. Texas Instruments, **TPS925302-Q1 dual synchronous LED driver**.
   https://www.ti.com/product/TPS925302-Q1
6. Analog Devices, **LT8376 — 60V, 3A Synchronous Step-Down LED Driver with
   Silent Switcher**.
   https://www.analog.com/en/products/lt8376.html
