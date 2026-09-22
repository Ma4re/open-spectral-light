# Prototype A — LM3409HV Branch Design

## 1. Purpose

This study turns the preferred Phase 1 power-stage architecture into one
representative, benchable constant-current branch.

The branch is intended to drive one Prototype A Thrive COB from the nominal
48 V internal bus.

This is still a **prototype design study**. It does not freeze a production
schematic, PCB layout, MOSFET, diode, inductor, connector, or final input range.

## 2. Design point

The representative high-power operating point is:

| Parameter | Design value |
|---|---:|
| Nominal input | 48 V |
| LED operating voltage | ~35.5 V typical |
| LED design envelope | ~31–42 V |
| Normal maximum average LED current | ~2.11 A |
| COB component maximum current | 2.34 A |
| Approximate LED power at nominal point | ~75 W |
| Preferred controller baseline | TI LM3409HV |

TI's LM3409HV evaluation design is a useful starting point because it already
demonstrates 48 V input, 42 V LED output, 1.5 A LED current, and approximately
400 kHz nominal switching frequency.

The Prototype A branch requires more current and therefore must be redesigned
rather than copied.

## 3. Constant-off-time target

The LM3409HV uses constant-off-time control.

Using the TI design equations with:

- COFF capacitor = 470 pF;
- internal effective capacitance term = +20 pF;
- assumed efficiency = 95 %;
- target nominal switching frequency = 400 kHz;
- Vin = 48 V;
- VLED = 35.5 V;

the calculated off-time resistor is approximately:

```text
R_OFF ~= 31.8 kOhm
```

A standard 1 % candidate is:

```text
R_OFF = 31.6 kOhm
C_OFF = 470 pF
```

At the nominal point this gives approximately:

```text
t_OFF ~= 0.551 us
f_SW  ~= 402 kHz
```

Because the controller is constant-off-time, switching frequency is not fixed.

With the same R_OFF/C_OFF pair and the first-order 95 % efficiency assumption:

| Vin | VLED | Approx. switching frequency |
|---:|---:|---:|
| 48 V | 31 V | ~507 kHz |
| 48 V | 35.5 V | ~402 kHz |
| 48 V | 41.2 V | ~204 kHz |
| 46 V | 41.2 V | ~121 kHz |
| 52 V | 31 V | ~589 kHz |

This wide but still ultrasonic operating range is one reason the multi-branch
EMI/beat behavior must be measured on hardware.

## 4. Inductor selection

For a buck branch in CCM, the LM3409HV off-time relation gives approximately:

```math
\Delta I_L = \frac{V_{LED} t_{OFF}}{L}
```

At the nominal design point:

| Inductance | Approx. ripple p-p | Ripple / 2.11 A |
|---:|---:|---:|
| 56 uH | ~0.349 A | ~16.5 % |
| 68 uH | ~0.287 A | ~13.6 % |
| 82 uH | ~0.238 A | ~11.3 % |

### 4.1 Baseline value

**56 uH** is the preferred first bench value.

It provides moderate ripple without requiring an unnecessarily large/high-DCR
inductor.

A concrete characterization candidate is Bourns `SRR1280-560M`:

- 56 uH +/-20 %;
- 2.6 A Irms maximum;
- 2.5 A typical saturation current;
- 100 mOhm maximum DCR;
- 12.5 x 12.5 x 7.5 mm.

This is not frozen for production.

At the low-inductance tolerance extreme (~44.8 uH), ripple rises to roughly
0.44 A p-p. The resulting peak inductor current near the 2.11 A average target
is close enough to the inductor's saturation rating that the part must be
checked thermally and dynamically rather than accepted from nominal values only.

A future production branch may justify a larger-current 56–68 uH part with
lower DCR.

## 5. Current-sense resistor and IADJ

For LM3409HV the peak-current threshold is approximately:

```math
I_{L,pk} = \frac{V_{ADJ}}{5 R_{SNS}}
```

and in CCM the average LED current is approximately:

```math
I_{LED} = \frac{V_{ADJ}}{5R_{SNS}} - \frac{\Delta I_L}{2}.
```

With:

- target average current = 2.11 A;
- nominal ripple = 0.349 A p-p;
- desired full-scale IADJ near 1.20 V;

the calculated sense resistance is approximately:

```text
R_SNS ~= 0.105 Ohm
```

A 0.105 Ohm, 1 % low-TCR current-sense resistor is therefore the first design
candidate.

At 2.11 A, including triangular ripple, dissipation is approximately:

```text
P_RSNS ~= 0.47 W
```

Use at least a **1 W-class sense resistor** for the first prototype and use
Kelvin sensing into CSP/CSN.

### 5.1 IADJ must fail safe

The LM3409HV IADJ pin must **not be left floating**. TI permits open IADJ as a
maximum-current operating condition, which is the wrong failure behavior for
this product.

For Prototype A:

- normal full-scale IADJ is approximately 1.20 V;
- the controller-facing analog source shall actively pull IADJ low on reset or
  loss of control;
- a hardware maximum-voltage ceiling/clamp shall prevent a firmware or connector
  fault from requesting current above the qualified branch limit;
- EN provides an independent disable path.

The exact clamp circuit remains OPEN until the CM-to-PSM analog interface is
selected.

## 6. Analog-dimming reality

TI specifies a 250:1 analog-dimming capability for the LM3409 family, but this
does **not** mean Prototype A can assume 250:1 linear continuous-current dimming
with the selected 56 uH branch.

At the nominal high-power design point, the approximate CCM/DCM boundary is:

```text
I_CCM_boundary ~= DeltaI_L / 2
               ~= 0.175 A
               ~= 8.3 % of 2.11 A
```

Below this point the branch enters discontinuous conduction and the simple CCM
current equation no longer describes the transfer accurately.

Therefore:

- analog dimming remains preferred throughout the useful CCM region;
- DCM behavior below roughly 5–10 % output must be characterized;
- a synchronized hybrid/PWM region may still be needed for deep dimming;
- no product minimum-dimming percentage is frozen yet.

This corrects any interpretation that the IC's 250:1 headline ratio directly
becomes the fixture's usable camera-grade analog dimming ratio.

## 7. PFET candidate and gate-drive budget

TI recommends external PFET voltage/current margin and emphasizes gate charge
because LM3409HV drives the PFET from its internal VIN-referenced regulator.

A useful first candidate is MCC `MCB40P10Y-TP`:

- P-channel;
- 100 V VDS;
- 40 A headline current rating;
- ~56 mOhm maximum RDS(on) at -10 V gate drive;
- ~40 nC maximum total gate charge at 10 V;
- D2PAK;
- active distributor-backed part.

It has substantially more voltage margin than required by a nominal 48 V bus
while avoiding the very high gate charge of some ultra-low-RDS(on) PFETs.

First-order gate-charge current:

```text
Qg = 40 nC

at 402 kHz:
Qg * f ~= 16.1 mA

at 589 kHz:
Qg * f ~= 23.6 mA
```

Adding controller quiescent demand still keeps the estimate near the practical
range of the LM3409HV internal VCC regulator, but this must be checked with
temperature and actual gate-charge behavior.

The PFET is a **simulation/bench candidate**, not a frozen BOM part.

## 8. Freewheel diode

A first diode candidate is ST `STPS5H100B`:

- Schottky;
- 100 V reverse-voltage rating;
- 5 A average-current class;
- active production part;
- DPAK.

The 100 V rating provides useful switching-node margin above the nominal 48 V
bus.

At the nominal voltage ratio the diode conducts for approximately one quarter of
the switching cycle. First-order diode dissipation is only a few tenths of a
watt, but it increases toward the lower end of the LED-voltage envelope.

Actual loss and temperature must be measured because forward voltage depends on
current and junction temperature.

## 9. Approximate branch loss budget

At approximately 48 V -> 35.5 V / 2.11 A:

### Inductor copper

Using 100 mOhm maximum DCR:

```text
P_L,DCR ~= 0.45 W
```

### Sense resistor

```text
P_RSNS ~= 0.47 W
```

### PFET conduction

Using approximately 56–62 mOhm RDS(on) as a room-temperature scale gives roughly
a few tenths of a watt of conduction loss at the nominal duty ratio. Hot
RDS(on) will be higher.

### Schottky diode

Expected first-order loss is approximately a few tenths of a watt, depending
strongly on VLED and temperature.

### Controller, gate drive, and switching

These remain to be established with the vendor macro-model and hardware.

The resulting arithmetic is consistent with a high-90-percent branch efficiency,
but **no branch-efficiency claim is frozen**. The existing 94–96 % whole-PSM
planning range remains intentionally conservative until simulation and bench
measurements include all switching and thermal effects.

## 10. Input capacitance

Following TI's EVM sizing method, at the nominal point:

```text
t_OFF ~= 0.551 us
T_SW  ~= 2.486 us
t_ON  ~= 1.935 us
```

For a 1 V local input-ripple design target:

```math
C_{IN,min} \approx \frac{I_{LED} t_{ON}}{\Delta V_{IN}}
\approx 4.1\,uF.
```

Using margin comparable to TI's evaluation-board approach places the first local
nominal capacitance target around **7 uF or more per branch**.

A practical first footprint strategy is multiple 100 V X7R ceramics, for
example a nominal 2 x 4.7 uF class, **only after checking effective capacitance
at ~48 V DC bias**.

The eight branches will also share system-level bulk capacitance after the input
protection stage. Local branch ceramics do not replace that common bulk design.

The LM3409HV VCC pin additionally requires at least the manufacturer-recommended
1 uF ceramic bypass to VIN.

## 11. Output capacitance and optical ripple

LM3409HV can operate without an output capacitor, and TI's 48 V / 42 V EVM uses
that mode.

Prototype A should nevertheless reserve an **optional output-capacitor
footprint** near the LEM/LED connection for characterization.

Why:

- without output capacitance, LED current ripple approximately follows inductor
  ripple;
- output capacitance can reduce instantaneous LED-current/optical ripple even
  though the inductor still carries the switching ripple;
- camera requirements concern the optical waveform, not merely inductor current.

The capacitor shall initially be treated as DNP/experimental until LED dynamic
resistance, wiring, startup behavior, and control interaction are measured.

## 12. Candidate UVLO window

Because the worst-case cold LED voltage approaches the 48 V bus, the branch
should not attempt full-power operation from a badly sagged source.

A useful first UVLO candidate is approximately:

```text
turn-on  ~= 46.5 V
turn-off ~= 44.5 V
hysteresis ~= 2.0 V
```

Using the LM3409HV UVLO equations:

```text
R_UV2 ~= 90.9 kOhm
R_UV1 ~= 2.49 kOhm
```

gives approximately that window.

This is **not yet the final head input specification**. Final thresholds must
include:

- supply tolerance;
- cable/connector voltage drop;
- common input-protection drop;
- cold COB voltage distribution;
- thermal behavior;
- desired derating rather than abrupt shutdown near dropout.

At 46 V input and a ~41.2 V LED branch, nominal constant-off-time frequency falls
toward ~121 kHz, illustrating why significant input sag should not be treated as
normal full-power operation.

## 13. First branch baseline

The first schematic/simulation pass should use:

| Function | First candidate |
|---|---|
| Controller | LM3409HV |
| Vin | 48 V nominal |
| VLED envelope | ~31–42 V |
| ILED normal maximum | ~2.11 A |
| COFF | 470 pF |
| ROFF | 31.6 kOhm, 1 % |
| Inductor | 56 uH baseline |
| Inductor example | Bourns SRR1280-560M for first characterization |
| RSNS | ~0.105 Ohm, >=1 W, low TCR, Kelvin sense |
| Full-scale IADJ | ~1.20 V |
| PFET example | MCC MCB40P10Y-TP, 100 V P-channel |
| Diode example | ST STPS5H100B, 100 V / 5 A Schottky |
| Branch local CIN | effective target >=~7 uF at operating bias |
| VCC bypass | >=1 uF ceramic to VIN |
| Output capacitor | optional characterization footprint |
| UVLO candidate | ~46.5 V rising / ~44.5 V falling |

None of the example passives/discretes are frozen as production BOM parts.

## 14. Required test points

The first branch PCB/simulation schematic should expose:

- protected branch VIN;
- switch node;
- PGATE;
- IADJ;
- EN;
- CSP;
- CSN;
- LED+;
- LED-;
- branch output current measurement point;
- local ground;
- optional output-capacitor nodes.

CSP/CSN are differential high-side current-sense nodes and layout must preserve
Kelvin sensing around RSNS.

## 15. Simulation status and required next simulation

TI publishes both TINA-TI and PSpice transient models for the LM3409 family.

This study has completed the analytical sizing pass and an **idealized
constant-off-time switched-current model**. The ideal model is useful for
checking conduction-mode transitions but does not model LM3409HV internal
delays, PFET/diode parasitics, gate drive, switching loss, or control-device
nonidealities.

At the nominal 56 uH / 0.105 Ohm branch, the idealized model confirms:

- ~2.11 A average current at ~1.20 V IADJ in CCM;
- the CCM/DCM boundary occurs near ~0.175 A average LED current;
- IADJ-to-average-current transfer becomes strongly nonlinear once DCM begins;
- analog control can continue below the CCM boundary, but its usable
  camera-grade range cannot be inferred from the advertised 250:1 ratio.

The model is deliberately **not** used as a switching-frequency validation,
because a lossless buck slope model does not reproduce the empirical efficiency
factor used by TI's constant-off-time design equations.

A compatible TI TINA-TI/PSpice execution engine is not available in the current
project execution environment, so this study does **not** claim a
vendor-macro-model transient result.


The next simulation shall instantiate the TI model and the selected branch
candidates to verify at minimum:

1. startup at nominal 48 V;
2. steady-state current at 31 V, 35.5 V, and ~41.2 V LED voltage;
3. current ripple and switching-frequency variation;
4. IADJ sweep through CCM and into DCM;
5. EN/PWM turn-on and turn-off behavior;
6. input sag toward UVLO/dropout;
7. open-LED behavior;
8. shorted/reduced-Vf load behavior;
9. PFET/diode voltage and current stress;
10. sensitivity to +/-20 % inductor tolerance and sense-resistor tolerance.

Only after that macro-model simulation should this branch become the basis of a
Rev A PSM schematic.

## 16. Decision gate

The LM3409HV branch remains the preferred baseline if simulation and bench work
show:

- stable 2.11 A regulation over the real LED-voltage range;
- acceptable dropout margin from the accepted 48 V input range;
- branch efficiency consistent with the head thermal budget;
- no problematic startup/current overshoot;
- useful analog-dimming behavior through the main video range;
- acceptable optical ripple;
- manageable EMI when eight branches operate together.

If the unsynchronized constant-off-time architecture causes unacceptable EMI or
temporal interaction, repeat the branch study with a synchronized fixed-frequency
controller before changing the rest of the LEM/PSM architecture.

## References

1. Texas Instruments, **LM3409/LM3409HV datasheet**.
   https://www.ti.com/product/LM3409HV
2. Texas Instruments, **AN-1953 LM3409HV Evaluation Board**.
   https://www.ti.com/lit/pdf/SNVA390
3. Texas Instruments, **LM3409 simulation models**.
   https://www.ti.com/product/LM3409
4. Bourns, **SRR1280 Series Shielded SMD Power Inductors**.
   https://www.bourns.com/docs/Product-Datasheets/SRR1280.pdf
5. STMicroelectronics, **STPS5H100 — 100 V / 5 A Schottky rectifier**.
   https://www.st.com/en/diodes-and-rectifiers/stps5h100.html
6. Digi-Key / MCC, **MCB40P10Y-TP — 100 V P-channel MOSFET**.
   https://www.digikey.com/en/products/detail/micro-commercial-co/MCB40P10Y-TP/15652834
