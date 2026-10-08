# Prototype A — TPS92205x Branch Design

## 1. Purpose

This study dimensions one representative Prototype A constant-current branch
around the newer TI TPS92205x 4 A family.

The objective is to decide whether TPS922054/TPS922055 should replace LM3409HV
as the first Rev A power-stage implementation.

The branch drives one COB. Eight identical branches form the full PSM.

No value in this study is a frozen production hardware contract until simulation
and bench measurements validate it.

## 2. Electrical design point

| Parameter | Design target |
|---|---:|
| Nominal bus | 48 V |
| LED typical voltage | ~35.5 V |
| LED characterization range | ~31–41.2 V |
| Full-scale LED current | ~2.11 A |
| Typical LED output power | ~75 W |
| Driver family | TPS922054 / TPS922055 |
| Preferred package | 14-pin VSON DMT |
| PSM branch count | 8 |

The 4 A TPS922054/055 variants can support the approximately 2.11 A normal
branch target. TI's cycle-by-cycle **switch** current limit is materially higher
than the COB's 2.34 A maximum. This is a device-fault protection feature, **not**
a permissible continuous LED-current setpoint or an independent COB safety
limit. Startup, command corruption, sense-network faults, and open/short load
responses must be evaluated against the actual emitter maximum.

The VSON DMT package is preferred for first hardware because TI publishes a
lower junction-to-ambient thermal resistance than the WSON alternative:

- VSON DMT: ~39.1 C/W;
- WSON DRR: ~47.4 C/W.

Actual thermal performance will depend strongly on the PCB copper, thermal pad,
vias, airflow, and neighboring branches.

## 3. Current-sense resistor

TPS92205x uses a 200 mV full-scale current-sense reference:

```math
R_{SENSE}=\frac{0.200}{I_{LED,FS}}
```

For 2.11 A:

```text
RSENSE = 0.200 / 2.11
       ~= 94.8 mOhm
```

Prototype target:

```text
RSENSE ~= 95 mOhm
rating >= 1 W
low TCR
Kelvin routing to CSP / CSN
```

At 2.11 A, DC dissipation is approximately:

```text
P_RSENSE ~= 0.42 W
```

A 1 W part gives useful thermal margin. A tighter tolerance may later improve
branch-to-branch matching, but there is no need to add per-branch electronic
trim before measurements show it is useful.

## 4. Switching-frequency comparison

TI explicitly notes that lower switching frequency generally improves efficiency
and thermal behavior.

Two practical candidates were compared:

### Candidate A — 400 kHz

```text
RFSET = 59 kOhm
L     = 47 uH
```

### Candidate B — 600 kHz

```text
RFSET = 38 kOhm
L     = 33 uH
```

The ripple calculation uses the TI buck design equation and a conservative
48 V +10 % input corner (52.8 V). The current-sense drop is included
approximately in the power-path voltage.

| Point | 400 kHz / 47 uH | 600 kHz / 33 uH |
|---|---:|---:|
| 31 V LED | ~0.679 A p-p / 32.2 % | ~0.645 A / 30.6 % |
| 35.5 V LED | ~0.615 A / 29.1 % | ~0.584 A / 27.7 % |
| 41.2 V LED | ~0.475 A / 22.5 % | ~0.451 A / 21.4 % |
| Worst first-order peak current | ~2.45 A | ~2.43 A |

Both are comfortably below the TPS922054/055 minimum internal current-limit
specification.

TI's 2026 dimming guidance describes roughly 20–60 % inductor ripple as a
reasonable design region for this family. The earlier 10–20 % target inherited
from the LM3409 study was therefore more conservative than needed.

### Decision

**400 kHz / 47 uH is the preferred first branch.**

Reasons:

- nearly the same ripple as 600 kHz / 33 uH;
- lower expected switching loss;
- better thermal direction according to TI;
- better duty-cycle margin near the highest LED voltage;
- no meaningful PCB-area win from 600 kHz when both candidate inductors use the
  same package family.

600 kHz remains an easy A/B configuration by changing RFSET and the inductor.

## 5. Inductor candidate

A useful same-footprint A/B pair is:

### 400 kHz baseline

CODACA `CSAB1265A-470M`

- 47 uH;
- +/-20 %;
- 5.3 A rated current;
- 7.5 A saturation current;
- 68 mOhm maximum DCR;
- ~13.8 x 12.8 x 6.3 mm;
- active, distributor-backed.

### 600 kHz comparison

CODACA `CSAB1265A-330M`

- 33 uH;
- +/-20 %;
- 5.3 A rated current;
- 8 A saturation current;
- 69 mOhm maximum DCR;
- same ~13.8 x 12.8 x 6.3 mm package class.

Because the physical size and DCR are essentially unchanged, the 600 kHz option
does not buy enough hardware compactness to justify its higher switching rate by
default.

At the nominal 400 kHz / 47 uH point, estimated inductor copper loss is about
0.30 W using the maximum published DCR.

The final production inductor remains open. Lower-DCR 47 uH parts may improve
efficiency after the branch topology is validated.

## 6. Duty cycle and low-input margin

The worst LED-voltage point is important because TPS92205x has a 100 ns minimum
switch off-time.

Ignoring losses, the required duty ratio is approximately:

```math
D \approx \frac{V_{LED}+V_{SENSE}}{V_{IN}}
```

At 41.2 V LED voltage:

### At 48 V bus

```text
D ~= 41.4 / 48
  ~= 86.3 %
```

This is comfortable.

### At 43.2 V bus (48 V -10 %)

```text
D ~= 41.4 / 43.2
  ~= 95.8 %
```

For comparison, a 100 ns minimum off-time implies idealized maximum duties near:

```text
400 kHz: ~96 %
600 kHz: ~94 %
```

Therefore TI's 48 V +/-10 % reference-input range must **not** automatically
become the OpenSpectralLight full-power input contract.

At the highest cold COB voltage, 43.2 V input leaves essentially no real
headroom once switch, inductor, sense, wiring, and protection losses are
included.

### Candidate head behavior

For Prototype A, treat approximately **46 V** as the first full-power minimum
input candidate and characterize derating/dropout below it.

The exact full-power input range remains OPEN.

This is another reason 400 kHz is preferred over 600 kHz.

The TPS92205x UVP pin is primarily part of output/load monitoring and CC/CV
behavior; the system-level 46–48 V input-floor policy should be implemented by
the common PSM/head input supervisor rather than misusing UVP as a high-voltage
bus undervoltage comparator.

## 7. Input capacitance

Using the TI input-ripple relationship at approximately:

```text
Vin(max) = 52.8 V
Vpath    ~= 35.7 V
I        = 2.11 A
fSW      = 400 kHz
```

the effective local capacitance required before ESR contribution is roughly:

| Allowed branch input ripple | Calculated effective CIN |
|---:|---:|
| 0.6 V | ~5.9 uF |
| 0.5 V | ~7.1 uF |
| 0.3 V | ~11.9 uF |

A practical first branch network is therefore similar to TI's reference
architecture:

- ~10 uF / 100 V local electrolytic or equivalent bulk element;
- 2.2–4.7 uF / 100 V X7R ceramic;
- 0.1 uF high-frequency ceramic.

The actual ceramic effective capacitance at ~48 V DC bias must be checked from
the selected capacitor data.

Eight local branch networks still require a common system-level bulk/input
network after the head protection stage.

## 8. Output capacitance and optical ripple

TPS92205x intentionally supports output capacitance to reduce LED-current ripple.

The previous 0.9 Ohm dynamic-resistance estimate was insufficiently supported.
The Bridgelux V18 typical DC Vf values around 1.755 A (about 35.6 V) and
2.34 A (about 36.6 V) instead imply a **secant slope of about 1.7 Ohm** over
that current interval. This is not guaranteed to equal the local small-signal
impedance at 400 kHz, especially with junction/device capacitance, temperature
and wiring parasitics.

At 400 kHz / 47 uH, first-order inductor ripple is approximately 0.615 A p-p
at the conservative input corner. The LED-current ripple cannot be guaranteed
from this DC secant slope alone.

Consequently, do not use the former 0.9-Ohm-based 0.9/2.3/5.0-uF lookup values
as verified capacitor requirements. Characterize several effective COUT levels
with the TI macro-model and real LED-current/optical-waveform measurements.

### Baseline

Target **~3–5 uF effective COUT** at the real DC bias.

A practical footprint experiment can begin with:

```text
2 x 4.7 uF / 100 V X7R nominal
+ 0.1 uF ceramic
```

after verifying the actual derated capacitance.

This **might** reduce COB-current ripple into the few-percent region; that is a
measurement hypothesis, not a guaranteed electrical or optical specification.

Do not oversize COUT blindly. TI's 2026 dimming application note demonstrates
that larger COUT reduces high-frequency ripple but slows PWM edge response
substantially.

The final COUT is therefore selected from **optical waveform + dimming-response**
measurements, not electrical ripple alone.

## 9. Compensation and sense filtering

Initial network should stay close to TI's current guidance:

- RFLT: 100 Ohm;
- optional CFLT: 1 nF footprint;
- CCOMP: 1 nF baseline;
- RCOMP: footprint, initially DNP unless simulation/bench requires it;
- RDAMP: footprint, initially DNP;
- RLK_COMP: footprint, initially DNP.

Why keep RDAMP unpopulated initially:

TI's 2026 application note shows that damping can suppress current overshoot but
can also degrade current accuracy and create a minimum usable analog-dimming
command.

Why reserve RLK_COMP:

At very low or off-state current, leakage through the LED path can cause faint
glow in some loads. The compensation resistor should be sized only after actual
COB leakage/off-state behavior is measured.

## 10. Freewheel diode

The branch remains non-synchronous and requires a Schottky/freewheel diode.

The existing ST `STPS5H100B` remains a practical first candidate:

- 100 V reverse rating;
- 5 A current class;
- active;
- DPAK;
- distributor-stocked.

At the nominal 35.5 V / 48 V point, the diode conducts during roughly 26 % of
the switching cycle.

For representative hot forward drops, first-order dissipation is around:

```text
~0.25–0.35 W
```

The 100 V rating also gives useful switching-node margin relative to the
65 V IC limit.

## 11. First-order loss budget

At approximately 48 V -> 35.5 V / 2.11 A:

### LED output

```text
P_LED ~= 74.9 W
```

### Sense resistor

```text
~0.42 W
```

### 47 uH inductor copper

Using 68 mOhm maximum DCR:

```text
~0.30 W
```

### Integrated switch conduction

Using the published 150 mOhm typical room-temperature RDS(on):

```text
~0.50 W
```

RDS(on) rises with junction temperature, so hot conduction loss will be higher.

### Schottky conduction

First-order estimate:

```text
~0.25–0.35 W
```

### Remaining losses

Still require vendor-model and bench determination:

- switch transitions;
- inductor core loss;
- IC quiescent/internal losses;
- capacitor ESR;
- PCB copper;
- ringing/snubber loss if required.

The known first-order terms total roughly 1.5 W before switching/core/internal
losses.

A sensible **early thermal-planning allowance is ~3–4 W loss per branch at
full branch power**, not an efficiency claim.

That corresponds to roughly 95–96 % branch efficiency if realized, which is
consistent with the general region of TI's typical 48 V efficiency data but must
be measured in our implementation.

For the full PSM, retain the earlier ~15–20 W-class converter thermal planning
budget until hardware data is available.

## 12. IC thermal design

The integrated MOSFET moves a meaningful fraction of branch loss into the
TPS92205x package.

Use the VSON DMT package first where procurement permits because its published
RthetaJA is lower than the WSON package.

Layout requirements:

- exposed thermal pad fully soldered;
- dense thermal-via array into internal/bottom copper;
- generous local copper;
- avoid stacking eight driver ICs thermally in one small PCB region;
- place inductors and Schottky diodes so their heat does not directly heat the
  IC thermal pad;
- keep the PSM in the head airflow.

### Internal thermal foldback

A useful first secondary-protection candidate is:

```text
RTEMP ~= 60 kOhm
TTH   ~= 100 C nominal
```

This is deliberately conservative and is **not** a substitute for the LEM's
TEMP_WW/TEMP_CW sensing or the controller's product thermal policy.

If foldback interferes with characterization, its threshold can be changed in a
controlled bench revision rather than disabling all thermal protection.

## 13. Dimming configuration

Use **flexible dimming** as the architecture baseline.

### Main video range

- EN/PWM held continuously active;
- ADIM/HD provides a PWM-coded digital command that changes the IC's internal
  analog current reference;
- the LED remains continuously current-regulated.

This avoids intentionally chopping the optical output merely to set normal
brightness.

### Deep dimming

If measurements show that analog-current quality becomes unacceptable at low
levels:

- keep the desired current ratio set through ADIM;
- use one shared temporal gate on EN/PWM across all active WW/CW branches;
- choose PWM frequency only after direct optical/camera measurement.

Do not automatically use the IC's built-in ~12.5 % hybrid crossover. That
crossover is a convenient device feature, not a product requirement.

## 14. TPS922054 versus TPS922055

Use the same branch PCB for both variants when the same package is selected.

### TPS922054

Preferred first population:

- spread spectrum disabled;
- TI explicitly positions the no-spread variant for better low-brightness
  performance.

### TPS922055

EMI comparison:

- +/-7 % frequency spreading;
- ~2 kHz spread-spectrum modulation.

At a 400 kHz center frequency, its switching energy is approximately spread over
372–428 kHz.

The current loop and output capacitor should reject most of this from the LED
current, but that must be **measured optically**.

The 2 kHz modulation itself is not accepted as camera-transparent merely because
it is above ordinary human flicker perception.

## 15. 400 kHz versus 600 kHz decision

| Property | 400 kHz / 47 uH | 600 kHz / 33 uH |
|---|---|---|
| Nominal current ripple | ~29 % | ~28 % |
| Inductor footprint candidate | same 1265 class | same 1265 class |
| DCR candidate | ~68 mOhm | ~69 mOhm |
| Duty margin near high VLED | **better** | lower |
| Switching loss | **lower expected** | higher expected |
| Passive size benefit | negligible with selected pair | negligible |
| EMI fundamental | lower | higher |
| Dimming transient potential | good | somewhat faster |

**Decision: 400 kHz / 47 uH is the Rev A branch baseline.**

600 kHz / 33 uH remains a resistor+inductor A/B option if EMI or transient
measurements reveal a real advantage.

There is currently no evidence that 600 kHz provides enough benefit to justify
its higher switching frequency.

## 16. Proposed Rev A branch baseline

| Function | Baseline |
|---|---|
| Driver | TPS922054DMTR first population |
| EMI comparison | TPS922055DMTR |
| Package | 14-pin VSON DMT |
| Vin nominal | 48 V |
| Full-power input floor candidate | ~46 V, to validate |
| Characterization Vin high corner | 52.8 V |
| LED voltage | ~31–41.2 V |
| LED full-scale current | ~2.11 A |
| RSENSE | ~95 mOhm, >=1 W, low TCR, Kelvin |
| Switching frequency | 400 kHz |
| RFSET | 59 kOhm |
| Inductor | 47 uH |
| First inductor candidate | CSAB1265A-470M |
| 600 kHz alternative | 38 kOhm + CSAB1265A-330M |
| Diode | STPS5H100B or equivalent 100 V / >=5 A Schottky |
| Local CIN | >=~7 uF effective target + HF bypass; start near TI-style 10 uF bulk + ceramic |
| Effective COUT target | ~3–5 uF |
| RFLT | 100 Ohm |
| CFLT | 1 nF optional footprint |
| CCOMP | 1 nF baseline |
| RDAMP / RCOMP | DNP footprints initially |
| RLK_COMP | DNP footprint initially |
| RTEMP | ~60 kOhm / ~100 C candidate secondary foldback |
| Control mode | flexible; analog-current first |
| FAULT | exposed to PSM/controller |
| Optical deep PWM | not enabled by default until validated |

## 17. Supply-chain check

The family satisfies the project's prototype-procurement rule:

- TPS922054/055 are active TI catalog parts;
- distributor stock exists in prototype quantities;
- the relevant 4 A variants are orderable without vendor approval;
- the 47 uH / 33 uH inductor candidates are active distributor parts;
- the ST 100 V / 5 A Schottky is active and widely stocked.

Exact production-source diversification remains a later BOM qualification step.

## 18. Decision versus LM3409HV

The detailed sizing does **not** show a dramatic raw-efficiency advantage over
LM3409HV.

LM3409HV can use a lower-RDS(on) external PFET and has 75 V input margin.

TPS92205x wins Prototype A on the **system trade**:

- integrated main switch;
- substantially lower branch BOM complexity;
- programmable near-fixed switching frequency;
- better fault visibility;
- configurable thermal foldback;
- flexible camera-oriented dimming modes;
- current simulation/application-note support;
- easy TPS922054/TPS922055 EMI A/B test;
- reference design almost identical to our electrical operating point.

Therefore:

> **TPS922054 at 400 kHz / 47 uH is the preferred Rev A branch baseline.**

LM3409HV remains the fallback if TPS92205x shows an unexpected thermal,
low-brightness, EMI, availability, or 65 V-margin problem.

## 19. Aggregate power and fail-safe contract

The complete logical safety contract and fault-injection/acceptance matrix
are documented in
[`prototype-a-power-and-fault-safety.md`](prototype-a-power-and-fault-safety.md).
TI datasheet Table 7-3 includes faults (LED terminal short and sense-resistor
short) that assert FAULT **while switching can continue**, so the external
fault-inhibit/latch is a required design study before relying on automatic IC
recovery.

A 300 W total LEM operating ceiling must not be inferred from the eight
individual 2.11 A current ceilings. Eight COBs at ~75 W each can nominally
request ~600 W before derating. The ~400 W-class source cannot sustain that
condition.

Required implementation evidence before full PSM replication:

- enforce the aggregate WW + CW electrical budget in normal control;
- bound each branch independently through validated analog setpoint/routing
  and fault behavior, not solely the controller's advertised switch OCP;
- implement a common input-current/power-limiting or safe-disable mechanism
  that prevents a persistent command/fault from overloading the external source;
- define and verify a fail-off behavior on controller reset, missing LEM,
  invalid module descriptor, lost setpoint signal, or thermal-sensor fault;
- verify FAULT propagation and latching/recovery policy for multiple branches.

The exact components, thresholds, and response times are OPEN. These are safety
and interface requirements; they must not be converted into arbitrary resistor
values before a circuit and fault analysis exist.

## 20. Remaining validation before replication x8

Before copying the branch eight times:

1. run the current TI PSpice/SIMPLIS model;
2. verify 31 V, 35.5 V, and 41.2 V LED operating points;
3. sweep input down toward the candidate ~46 V full-power floor;
4. verify startup and output-current overshoot;
5. characterize analog dimming from 100 % toward the useful low-current limit;
6. compare COUT values using electrical and optical ripple;
7. compare TPS922054 against TPS922055 on equivalent hardware;
8. measure IC, inductor, diode, and sense-resistor temperatures;
9. verify FAULT behavior for open/short load and sense faults;
10. measure conducted/radiated EMI with one branch, then multiple branches;
11. verify no meaningful optical 2 kHz component appears with TPS922055;
12. validate real efficiency rather than relying on typical datasheet curves.

Only then should this branch be replicated into the eight-channel Rev A PSM.

## References

1. Texas Instruments, **TPS92205x 65V 2A / 4A Buck LED Driver with Inductive
   Fast Dimming**, Rev. B.
   https://www.ti.com/lit/ds/symlink/tps922054.pdf
2. Texas Instruments, **Optimizing Illumination LED Driver Design for Best
   Dimming Performance with TPS92205x and TPS92365x**, 2026.
   https://www.ti.com/lit/pdf/SDAA392
3. Digi-Key, **CODACA CSAB1265A-470M**, 47 uH / 5.3 A / 68 mOhm.
   https://www.digikey.com/en/products/detail/codaca/CSAB1265A-470M/16731479
4. Digi-Key, **CODACA CSAB1265A-330M**, 33 uH / 5.3 A.
   https://www.digikey.com/en/products/detail/codaca/CSAB1265A-330M/16731437
5. STMicroelectronics, **STPS5H100**, 100 V / 5 A Schottky.
   https://www.st.com/en/diodes-and-rectifiers/stps5h100.html
6. Bridgelux, **V18 Thrive / DS322 family data**, used for the Prototype A COB
   voltage/current and first-order dynamic-resistance estimate.
   https://www.bridgelux.com/thrive
