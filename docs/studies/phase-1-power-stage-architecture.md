# Phase 1 Power-Stage Architecture Study

## 1. Purpose

This study selects the preferred **prototype power-stage architecture** for
Prototype A without yet freezing a driver IC, switching frequency, connector, or
production schematic.

Prototype A currently requires:

- 2 logical roles: WW and CW;
- 4 physical branches per role;
- 8 independently current-regulated COB branches total;
- approximately 31–42 V branch compliance;
- approximately 2.11 A normal prototype maximum per active branch;
- approximately 300 W aggregate LEM operating ceiling.

The power-stage decision must also preserve the project requirements for
camera-safe dimming, replaceable light-engine modules, and external DC power.

## 2. Options compared

### A. Eight independent buck constant-current branches

One buck current regulator drives each COB.

At the product-control level the eight branches remain grouped into only two
logical roles:

```text
WW command -> 4 regulated WW branches
CW command -> 4 regulated CW branches
```

**Advantages**

- no passive current-sharing assumption between COBs;
- branch current is bounded independently;
- one open/short/fault does not force uncontrolled redistribution into the
  remaining COBs;
- simple buck conversion from a suitable DC bus;
- each branch is electrically identical, simplifying design reuse and testing;
- naturally supports the replaceable LEM branch contract;
- analog dimming can preserve continuous emission over the principal video
  range;
- fault isolation and branch characterization are straightforward.

**Disadvantages**

- eight inductors and eight switching stages;
- larger PSM PCB than a two-channel design;
- switching/EMI behavior from multiple converters must be characterized;
- branch current matching depends on regulator/sense accuracy.

**Assessment:** preferred Phase 1 architecture.

### B. Two high-current bank regulators feeding four parallel COBs each

Each logical WW/CW role would use one approximately 8.4 A regulator and place
four COBs in parallel.

**Advantages**

- only two main power converters;
- fewer inductors/controllers.

**Disadvantages**

- COB forward-voltage spread causes unequal branch currents;
- LED forward voltage changes with temperature, so passive imbalance can worsen
  thermally;
- ballast resistance large enough to force useful sharing wastes power and adds
  heat;
- active current balancing largely recreates independent branch regulators;
- one branch failure redistributes bank current unless additional protection is
  added.

**Assessment:** rejected for Prototype A.

### C. Two-series / two-parallel strings per CCT bank

Two COBs are placed in series per string and two strings are used per bank.

**Advantages**

- only four regulated strings total;
- reduced converter count.

**Disadvantages**

- cold LED string voltage approaches approximately 82 V;
- a pure buck implementation therefore needs a bus well above the current
  low-voltage architecture;
- a 48 V source would instead require boost/buck-boost conversion;
- higher-voltage bus changes connectors, protection, clearance, power-source,
  service, and battery assumptions;
- two parallel strings still require controlled current sharing.

**Assessment:** not justified for Phase 1.

### D. Integrated multi-channel automotive LED-driver ICs

Modern dual-channel synchronous devices can reduce external switch count and add
SPI diagnostics.

A representative example is TI TPS92520-Q1, a 4.5–65 V dual synchronous buck
driver with up to 1.6 A continuous output per channel. TI also documents
paralleling its channels for higher current.

**Advantages**

- synchronous conversion;
- rich diagnostics;
- compact integrated solution;
- digital configuration.

**Disadvantages for Prototype A**

- 1.6 A per native channel is below the approximately 2.11 A branch target;
- channel paralleling is required for each COB;
- this does not reduce the number of IC packages enough to offset complexity;
- integrated thermal density becomes significant at roughly 75 W processed per
  COB branch;
- analog dimming range is less attractive than the preferred discrete-controller
  candidate.

**Assessment:** useful alternative technology, not the Prototype A baseline.

## 3. DC-bus comparison

### 36 V class

Rejected.

The Thrive branch cold design voltage can exceed 40 V, so a 36 V bus cannot
provide full-output buck regulation.

### 48 V class

Strongly preferred for Prototype A.

At the approximate operating points:

```text
Vbus       = 48 V nominal
VLED typ   = ~35.5 V
VLED cold  = up to ~41.2 V design reference
Ibranch    = up to ~2.11 A normal prototype target
```

The corresponding ideal buck duty ratio is approximately:

```text
35.5 / 48 = 74 %
41.2 / 48 = 86 %
```

This leaves useful buck headroom without requiring a high-voltage external
source.

The choice is strongly supported by an existing TI LM3409HV evaluation design
that operates from **48 V input to a 42 V LED load at 1.5 A and approximately
400 kHz**. That is very close to the voltage ratio required by Prototype A.

48 V is also a normal commercial power-supply class. Current 400 W-class
supplies exist around 48 V / 8.3 A, and professional cinema lights also use 48 V
DC distribution at substantially higher power.

### 54–60 V class

Technically possible and provides additional buck headroom, but not preferred
for the first prototype because:

- 48 V already supports the branch voltage target;
- switching loss generally rises with unnecessary input voltage;
- the supply/battery ecosystem is less convenient;
- many useful 60–65 V LED controllers would have less transient margin.

### ~90 V class

Rejected for Phase 1 because it exists mainly to make 2S strings convenient.
It increases system-voltage complexity without solving a product requirement.

## 4. Prototype power budget

The LEM target is approximately 300 W electrical.

Using an initial PSM efficiency planning assumption of 94–96 %:

```text
300 W LED / 0.94 = ~319 W bus power
300 W LED / 0.96 = ~313 W bus power
```

Allowing additional head loads for:

- cooling fan(s);
- controller;
- display/local UI;
- sensor/auxiliary rails;
- margin;

places the first full-output external source naturally in the **~400 W class**.

At 48 V:

```text
400 W / 48 V = 8.33 A
```

Therefore a **48 V nominal, approximately 400 W source class** is the preferred
Prototype A power envelope.

This does not freeze the final input connector or exact minimum source rating.

A future battery solution should present a regulated source compatible with the
head input envelope. Phase 1 does not require direct connection of an arbitrary
raw battery stack whose voltage can fall below the LED branch requirement.

## 5. Preferred branch-controller direction

The strongest current baseline candidate is **TI LM3409HV**.

Relevant manufacturer-published characteristics:

- active product;
- 6–75 V input range;
- buck LED-current controller;
- external PFET allows branch power/current scaling;
- up to 5 A LED current is supported by the controller class;
- differential high-side current sensing;
- cycle-by-cycle current limiting;
- no loop-compensation network required;
- 250:1 analog dimming range;
- 10,000:1 PWM dimming capability.

Most importantly, TI's LM3409HV evaluation board already demonstrates:

- 48 V input;
- 42 V LED output;
- 1.5 A LED current;
- approximately 400 kHz nominal switching frequency.

Prototype A needs a higher 2.11 A branch current, so the evaluation board cannot
be copied unchanged. The PFET, diode, inductor, current-sense resistor, thermal
layout, and protection values must be redesigned.

The EVM is valuable because it validates the **topology and voltage ratio**, not
because it is a ready-made final branch.

## 6. Alternative branch controllers

### TI TPS92691

Relevant features:

- active;
- 4.5–65 V input;
- up to 5 A controller class with external power stage;
- analog and PWM dimming;
- fixed switching frequency with external synchronization;
- LED current monitor output;
- open/short diagnostic support;
- better than approximately ±3 % LED current accuracy over its stated range.

Its major trade-off for this project is analog dimming range: approximately
15:1. That would likely move deep dimming into PWM sooner than the LM3409HV.

TPS92691 is the preferred **diagnostics/synchronization alternative** if
multi-converter beat/EMI behavior or observability becomes more important than
wide analog dimming range.

### TI TPS92692

Similar multi-topology controller with current monitoring, fault reporting,
synchronization, and spread-spectrum support.

It also provides only approximately 15:1 analog dimming, so it carries the same
camera-light trade-off.

### Analog Devices LT3761A

Provides analog/PWM dimming and multiple converter topologies, but its 60 V input
ceiling is less attractive around a 48 V nominal bus with protection/transient
margin, and its flexibility is unnecessary for a straightforward buck branch.

## 7. Why LM3409HV is only a prototype baseline

The LM3409HV uses constant-off-time control and does not provide the same
straightforward external clock synchronization as some alternatives.

With eight independent switching branches, this creates two questions that
cannot be answered from the datasheet alone:

1. Will unsynchronized converter frequencies produce unacceptable conducted or
   radiated EMI?
2. Will the summed optical current ripple create any measurable low-frequency
   envelope/beat behavior relevant to camera exposure?

These are bench questions.

The Prototype A PSM should therefore make the branch power stage measurable and
leave enough test access to compare at least one synchronized alternative if the
LM3409HV approach fails temporal or EMI validation.

No driver IC is frozen by this study.

## 8. Proposed control hierarchy

The PSM should preserve the existing two logical roles despite eight physical
power branches.

Conceptually:

```text
Controller request
      |
      +-- WW current command ------+--> WW0
      |                            +--> WW1
      |                            +--> WW2
      |                            +--> WW3
      |
      +-- CW current command ------+--> CW0
                                   +--> CW1
                                   +--> CW2
                                   +--> CW3
```

Normal operation does not require eight user-visible current settings.

The PSM may use one common analog setpoint per logical role to drive four
high-impedance branch-control inputs, provided the resulting branch-current
matching is validated.

Per-branch trim should not be added until measurements demonstrate that normal
regulator/sense tolerances cause a meaningful optical-uniformity problem.

This avoids unnecessary eight-channel calibration hardware.

## 9. Dimming signals

The preferred logical controls are:

- WW analog-current command;
- CW analog-current command;
- global output enable;
- common synchronized deep-dimming gate if PWM/hybrid operation is needed.

Normal CCT control remains concurrent:

```text
WW current > 0
CW current > 0
at the same time
```

If deep PWM dimming is needed, **all active branches should receive the same
global PWM timing** so the instantaneous WW/CW ratio is preserved.

Independent PWM clocks per branch are not a design target.

The exact CM-to-PSM electrical/protocol interface remains OPEN. For a bench
prototype, direct analog setpoints and logic enables are acceptable. A final
replaceable PSM interface should not expose driver-IC-specific control details
to product firmware.

## 10. Branch ripple target

A switching LED driver necessarily contains high-frequency current ripple.

For the first branch design, a sensible engineering target is to keep inductor/
LED current ripple approximately in the **10–20 % peak-to-peak** range at the
high-power nominal point and then measure the actual optical waveform.

At approximately:

```text
Vin = 48 V
Vout = 35.5 V
Iout = 2.11 A
fsw ~ 400 kHz
```

a first-order buck calculation gives roughly:

- ~110 uH for approximately 10 % p-p current ripple;
- ~55 uH for approximately 20 % p-p current ripple.

TI's 48 V -> 42 V / 1.5 A evaluation design uses 33 uH at its own operating
point, confirming that practical inductance is strongly dependent on selected
ripple, voltage ratio, and current.

No inductor value is frozen yet.

The camera requirement is based on measured optical modulation, not on an
arbitrary electrical ripple percentage.

## 11. Input protection boundary

The PSM/head shall not connect the external 48 V source directly to the eight
bucks without a defined input-protection stage.

The later hardware design must evaluate:

- branch/system fuse strategy;
- reverse-polarity protection;
- input transient/TVS protection;
- inrush control for bulk capacitance;
- undervoltage lockout;
- overvoltage behavior;
- input current measurement if useful.

The input-protection stage is common to the head and should not be duplicated
eight times.

Exact parts and thresholds remain OPEN.

## 12. Thermal consequence

At approximately 300 W LED power and 94–96 % converter efficiency, the PSM
itself dissipates roughly:

```text
~13–19 W
```

before auxiliary loads.

That is materially smaller than the LEM thermal load, but it is not negligible.

The PSM should:

- live in the head airflow;
- remain thermally separate from the LEM standardized thermal plane where
  practical;
- spread its eight inductors/MOSFETs/diodes rather than concentrate all heat in
  one small region;
- expose at least one board-temperature measurement point for characterization.

## 13. Recommended Prototype A PSM architecture

The current preferred prototype is:

```text
External 48 V DC
       |
  input protection
       |
  48 V internal bus
       |
       +-- Buck WW0 --> COB WW0
       +-- Buck WW1 --> COB WW1
       +-- Buck WW2 --> COB WW2
       +-- Buck WW3 --> COB WW3
       |
       +-- Buck CW0 --> COB CW0
       +-- Buck CW1 --> COB CW1
       +-- Buck CW2 --> COB CW2
       +-- Buck CW3 --> COB CW3

Controller:
  WW analog command -> WW0..WW3
  CW analog command -> CW0..CW3
  GLOBAL_ENABLE     -> all branches
  GLOBAL_DIM        -> all active branches when hybrid/PWM is used
```

Baseline branch-controller candidate: LM3409HV or an equivalent distributor-
backed high-current buck controller meeting the same functional envelope.

## 14. Candidate PSM-TW2 envelope

Prototype work may now use the following **candidate** envelope:

| Property | Candidate value |
|---|---|
| External DC class | 48 V nominal |
| Full-output source class | approximately 400 W |
| Approximate full-output bus current | <= ~8.3 A source class |
| Logical roles | 2: WW, CW |
| Max physical branches per role | 4 |
| Total physical branches | 8 |
| Branch LED voltage envelope | approximately 31–42 V |
| Normal branch current target | 0–approximately 2.11 A |
| LEM aggregate operating ceiling | approximately 300 W |
| Preferred conversion | independent buck constant-current branch |
| Normal dimming | analog/current control |
| Deep dimming | optional common synchronized PWM/hybrid after validation |
| Branch hot-swap | not supported |
| Raw battery direct-connect | not assumed |

These numbers are not yet a versioned hardware contract.

## 15. Decision before freezing 48 V

48 V is now the preferred **TARGET** bus because it:

- clears the Prototype A branch-voltage envelope;
- has direct high-power LED-driver reference evidence;
- keeps head current near a manageable 7–8 A class;
- is available in normal commercial 400 W power supplies;
- is already used in professional cinema-light power distribution.

Before 48 V becomes FROZEN, the project should verify:

1. full-load branch dropout margin at the highest cold COB voltage;
2. input-cable/connector voltage drop at the selected current;
3. protection-stage drop;
4. PSM efficiency and thermal behavior;
5. external supply tolerance/ripple;
6. whether the eventual battery accessory should regulate to 48 V or use another
   compatible source range.

## 16. Immediate next step

Design and simulate **one representative 48 V -> 31–42 V / 2.11 A buck branch**
around the LM3409HV baseline.

That branch study should determine:

- switching-frequency target;
- current-ripple target;
- inductor value/current rating;
- PFET and diode stress/loss;
- sense-resistor value/power;
- analog-IADJ mapping;
- UVLO/dropout behavior;
- expected efficiency;
- thermal loss per branch;
- measurable test points.

Only after one branch is credible should it be replicated eight times.

## References

1. Texas Instruments, **LM3409HV — 75-V PFET buck controller for high-power LED
   drivers**.
   https://www.ti.com/product/LM3409HV
2. Texas Instruments, **AN-1953 LM3409HV Evaluation Board**.
   https://www.ti.com/lit/pdf/SNVA390
3. Texas Instruments, **TPS92691 — multi-topology LED driver with rail-to-rail
   current-sense amplifier**.
   https://www.ti.com/product/TPS92691
4. Texas Instruments, **TPS92692 — high-accuracy LED controller with spread
   spectrum frequency modulation**.
   https://www.ti.com/product/TPS92692
5. Texas Instruments, **TPS92520-Q1 — dual 1.6-A synchronous buck LED driver**.
   https://www.ti.com/product/TPS92520-Q1
6. Analog Devices, **LT3761A — 60 V input LED controller**.
   https://www.analog.com/en/products/lt3761a.html
7. Mean Well, **ERPF-400-48 — 48 V / 8.3 A 400 W-class power supply**.
   https://www.meanwell.com/Upload/PDF/ERPF-400/ERPF-400-SPEC.PDF
8. Aputure, **LS 600x Pro power options**, 48 V / 15 A external DC reference.
   https://help.aputure.com/en/ls600x-pro/power-options
