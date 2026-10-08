# Prototype A — Minimum PSM Protection Architecture Comparison

**Status:** PRE-DECISION ENGINEERING STUDY — preferred architecture, not a frozen
schematic, certified design, completed simulation or hardware test.

**Date:** 2026-10-08

**Scope:** TPS922054/TPS922055 current-regulated branches, common nominal 48 V
DC input, CM–PSM–LEM safety boundary. This study implements the intent of the
[Power and Fault Safety Contract](prototype-a-power-and-fault-safety.md) and
[ADR-0001](../adr/0001-power-envelope-policy.md) without inventing a fixed
300 W hardware trip.

## 1. Decision in one page

**Preferred minimal direction for the first protected prototype:**

1. A normally-off, **PSM-owned hardware inhibit** on each branch EN/PWM path.
2. A small **external watchdog** (window watchdog preferred if practical)
   driven by deliberate controller-health progress, plus CM arming.
3. A **hardware fault latch** driven by TPS92205x FAULT and safety-critical
   permissives, independent of firmware polling.
4. An **input-side protection / disconnect stage** with independent UV/OV and
   input-overcurrent response, using appropriately rated external MOSFET(s).
5. The existing correctly specified **current-sense network** as the normal
   branch current ceiling, with extra comparator/current cutoff added only if
   single-fault testing shows the TPS FAULT + inhibit does not adequately
   bound COB transient energy.
6. Safe LEM identity/temperature authorization, including open/short sensor
   detection and deliberate rearm.

This is a **comparison and selection for subsequent circuit design**, not a
decision to purchase any specific supervisor, fuse, MOSFET or resistor today.

**No exact 300 W hardware clamp.** The CM's normal power allocator uses the
currently *qualified* LEM/PSM/source/thermal envelope, potentially above the
initial ~300 W characterization point. Independent electrical protection is
dimensioned from real component and supply limits.

## 2. Electrical and failure anchors

| Item | Current reference | Status |
|---|---|---|
| External DC | ~48 V nominal | TARGET; input range OPEN |
| First branch | TPS922054 400 kHz / 47 uH | Preferred study direction |
| Current setpoint | ~2.11 A / COB | Initial prototype target |
| COB published current maximum | 2.34 A | Part-specific limit; must honor worst-case tolerances |
| Sense reference | ~200 mV / ~95 milliohm | Analytical nominal; tolerances/startup OPEN |
| Installed branches | 4 WW + 4 CW | Prototype A |
| Initial LED characterization | ~300 W | **Not** a hard ceiling |
| Initial source candidate | ~400 W at 48 V | **Not** rated for all-eight full command |
| Installed demand at all eight ~75 W | ~600 W LED, plus PSM losses | Fault/upgrade scenario; not a qualified mode |

The TI TPS92205x protection truth table (datasheet SLVSGG9B Rev. B,
Table 7-3) is decisive:

- LED output terminals shorted: **FAULT low; switching continues with
  minimum on-time**.
- Current-sense resistor shorted: **FAULT low; switching continues under
  the cycle-by-cycle switch-current limit**.
- LED open, sense resistor open, FET open/short and thermal shutdown:
  the IC stops switching in the listed detection condition, with recovery
  behavior defined by the device.
- The 4 A driver's switch-level current protection is **not** a
  COB-safe 2.34 A current limit.
- EN/PWM low disables the driver; FAULT is open drain (active low).

Therefore **FAULT is an alarm, not always a self-disconnecting protection**.
The PSM must own the actual disable mechanism.

## 3. Mandatory safety invariants

- All LEDs OFF on power-up, controller reset, bootloader, brownout, unplugged
  CM controls, missing LEM, invalid module descriptor, or invalid critical
  temperature sensor.
- EN/PWM low at the **physical driver pins** until every permission is valid;
  a floating input or MCU pull configuration cannot be the only safeguard.
- A continuously stuck-high CM ARM or PWM/setpoint output is insufficient to
  keep LEDs armed without valid watchdog freshness.
- A TPS FAULT that indicates a hazardous condition triggers **hardware
  inhibit/latch** even when its internal regulator continues switching.
- No automatic hazardous-fault retry. Rearm must be deliberate with
  conditions restored; startup must not silently clear a still-present fault.
- The input protection can isolate an unsafe load even when an IC's normal
  EN/PWM path becomes ineffective; stored bulk/output energy still needs
  explicit analysis.
- The CM's accurate operating wattage budget and the PSM's independent
  electrical overload cutoff are separate functions.
- A source upgrade cannot be inferred from a higher-power LEM descriptor.
  Source/cable/connector capability must be qualified independently.
- No energized LEM exchange in Phase 1.

## 4. Options: controller-liveness supervision

| Option | Cost/complexity | Failure coverage | Result |
|---|---|---|---|
| MCU IWDG/reset only | Low | Helps reset MCU but not necessarily an output held on during reset or failed app path | Insufficient alone |
| Firmware toggles GPIO, no external timeout | Lowest | Pin can freeze enabled | Reject alone |
| Simple retriggerable external watchdog | Low | Missing heartbeat / stuck static high or low; dependent on pulse-generation quality | Viable |
| **External window watchdog** | Low–moderate | Missing pulses and implausibly fast/stuck pulse pattern; more meaningful heartbeat | **Preferred** |
| Second programmable safety MCU | High | Potentially richer checks but new firmware/software lifecycle and common-cause issues | Not justified |

Manufacturer-backed example candidates (NOT selections):

- TI **TPS3431**: standalone programmable external watchdog with active-low
  open-drain output.
- TI **TPS3430**: window watchdog with programmable timeout and delay.
- TI **TPS3850**: window watchdog plus supervisor for a *low-voltage rail*;
  it does **not** directly supervise 48 V without a rated divider/front end.

The watchdog is supplied from a properly monitored low-voltage rail. Its
startup/disable defaults, WDO polarity, pullup and timeout tolerance require
schematic review. **Do not use its watchdog-disable mode as a normal operating
condition**.

**Required CM behavior:** refresh watchdog only after a meaningful control
iteration has completed its critical checks (LEM validity, temperature validity,
fault state and power allocation). A free-running timer peripheral or ISR that
can keep toggling while the application is stuck must not be treated as proof
of controller health. This does not prove complete software correctness.

## 5. Options: shutdown and fault handling

| Option | Pros | Critical weakness | Verdict |
|---|---|---|---|
| CM polls FAULT and sets EN low | Easy telemetry | Hangs/latency; cannot contain loss of firmware | Reject as sole protection |
| Common wired-OR FAULT signal only | Low pin count | Open-drain alarm does not intrinsically disable switching | Reject alone |
| **Hardware fault latch + dominant EN clamp** | Shared/simple; prevents automatic restart; preserves branch topology | Common gate failure and fault capture timing need analysis | **Preferred first path** |
| One independent latch per branch | Local fault isolation | Much more logic, rearm/debug, wiring; not justified until degraded mode is required | Defer |
| Upstream source disconnect only | Can cut input energy | Overkill for routine dimming; bulk output remains; may also kill fan/CM | Secondary, not primary |

### 5.1 Proposed logical interlock

```text
CM_ARM  & WATCHDOG_OK & LEM_VALID & TEMP_OK & SOURCE_OK
                                     & NOT(FAULT_LATCHED)
                                        |
                                        v
                             PSM_OUTPUT_PERMIT
                                        |
                     +------------------+----------------- ...
                     |                  |
                   branch 0           branch 1 ... branch 7
                  EN/PWM gate        EN/PWM gate
```

This is a **Boolean contract, not an electrical schematic**. The practical
implementation must ensure an independent disable transistor or logic gate
can force EN/PWM low **despite** a CM pin stuck high. Confirm low-voltage logic
levels, pull resistors, MCU output contention protection, power sequencing
and common-ground paths.

For normal analogue-current dimming, EN/PWM may stay high continuously while
ADIM/HD encodes the requested analogue current. If future deep PWM gating is
used, the hardware inhibit must override each PWM pulse asynchronously.
Do not solve this by tying all EN/PWM pins together with unqualified push-pull
MCU outputs.

### 5.2 Fault combining without accidental masking

TPS92205x FAULT is open-drain active-low. A wired-OR equivalent can be created
by pulling the shared line high at a safe logic voltage and allowing **any**
branch to sink it low, subject to the manufacturer's output rating and signal
integrity.

Important caveats:

- A high-impedance/unpowered FAULT may read as healthy. Combine with branch
  power-good / PSM power-good; do not interpret a pullup alone as valid health.
- Some FAULT states may disappear on EN disable. Use a latch if hazardous
  conditions must remain visible after shutdown.
- Confirm that disabling a branch does not itself generate a benign FAULT
  that prevents every future arm; qualify startup/settling timing without
  masking genuine faults.
- Electrical wiring, pullup current, capacitance and branch fault latency
  must be characterized for eight attached drivers.
- Record fault source through **per-branch test/diagnostic access** in the bench
  prototype even if the product initially uses only a common hardware latch.
- CM telemetry/logging is helpful but **does not replace** the hardware latch.

The global inhibit is allowed to disable all eight branches at once on a serious
fault. This is simpler and preserves color uniformity compared with running one
missing COB in an otherwise active CCT bank.

### 5.3 Rearm

Suggested logical rearm preconditions:

- all fault causes have cleared;
- actual physical outputs confirmed off;
- module/sensors/supply valid;
- the external watchdog has established a valid freshness state;
- a separate **deliberate arm action**, not just power or FAULT returning high.

Whether hazardous faults require a power cycle, user command or service action
remains fault-class-dependent and OPEN. Avoid a recurrent PSU hiccup loop.

## 6. Options: external 48 V input protection

| Option | Advantages | Limitations | Verdict |
|---|---|---|---|
| Fuse + TVS + PSU internal OCP only | Lowest BOM | No qualified programmable input overload shutdown; may hiccup/restart | Insufficient alone |
| Integrated eFuse/hot-swap | Can combine inrush/OC/OVP | Must satisfy **actual** source current, SOA, 48 V tolerance/transients and package heat | Evaluate when rating fits |
| Discrete shunt+comparator+MOSFET | Flexible current and latch | Designing reliable hotplug, gate drive, FET SOA and reverse behavior is substantial work | Alternate if justified |
| **Dedicated protector controlling external MOSFETs** | Built-in UV/OV, reverse/inrush options, independent breaker behavior; scalable FET current | External FETs, sense R, SOA/capacitive load and thresholds still need design | **Preferred candidate class** |

### 6.1 Concrete manufacturer-backed example: LTC4368

Analog Devices **LTC4368** provides a useful topology reference:

- operates across approximately **2.5–60 V** in normal specified operation;
- specifies **100 V overvoltage protection/survivability**, which is **not**
  permission to operate its protected load continuously at 100 V;
- controls **two back-to-back external N-channel MOSFETs**;
- adjustable UV and OV comparator thresholds;
- bidirectional electronic circuit breaker with approximately **50 mV forward
  current-sense threshold**;
- selectable forward overcurrent **latchoff or automatic retry**;
- SHDN control and FAULT indication.

Its family data and demo design establish that such a protector class is real,
not hypothetical. The design must still analyze external MOSFET SOA, fast
transients, input protection hierarchy, thermal layout and turn-on/inrush.
Latchoff is the preferred behavior for hazardous continuous overload.

**Illustrative sanity arithmetic only**: a 50 mV sense threshold around 8.3 A
would suggest ~6 milliohm. That **is not a recommended setting**; 8.3 A is the
nominal current of the first 400 W-class supply and leaves no tolerance, inrush
or operating margin. The threshold and shunt must instead follow the actual
**qualified** supply, source droop, auxiliary draw, sensor errors, thermal
conditions and prospective higher-power modes.

A higher continuous source class may require another shunt, FET/cable/connector
class and modified input protection; do not advertise that the 400 W prototype
harness can safely carry an upgraded 600+ W load.

### 6.2 Power-path partition is a hardware decision

A source-isolation switch should normally remove the dangerous **high-power
LED branch bus**. Retaining a small protected low-voltage supervisor/diagnostic
rail can make the latch state observable even after high-power shutdown.

But a cooling fan may need to continue during thermal cooldown; do not
automatically cut all cooling power with the same eFuse.

Topology questions to resolve before circuit capture:

- location of safety auxiliary rail and its own protection/fusing;
- whether the fan remains powered on a thermal trip;
- whether the input breaker is before/after shared bulk capacitors;
- bulk and COUT discharge, hotplug/reconnect current and stored-energy limits;
- whether a failed-shorted main switch can still feed a branch, and which
  independent path isolates that energy;
- physical connector and cable rated current, reverse polarity and touch safety;
- source-specific qualified current envelope without reliance on untrusted
  LEM metadata.

## 7. Options: per-COB current fault containment

The normal branch control is currently **not a simple analogue voltage
setpoint**. TPS92205x uses PWM-coded ADIM/HD to select an *internal analogue
current reference*. Do not copy the LM3409HV IADJ-voltage-clamp proposal into
this design.

### 7.1 First minimal path

For a correctly assembled, functioning sense network:

```text
I_LED,FS ~= 0.200 V / R_SENSE
```

Approximately 95 milliohm gives the original approximately 2.11 A nominal
command limit. The DIM duty command cannot intentionally request more than
the internal full-scale current **while sense resistance and control operate
as specified**.

The 2.34 A manufacturer maximum is **not a robust fault threshold**. Quantify
sense tolerance/TCR, current-reference error, current ripple, startup,
inductor/output-capacitor transient currents and shorted-sense behavior.
The acceptable electrical margin must be derived from those worst cases.

### 7.2 Alternatives if testing demonstrates a gap

| Path | Benefit | Limitation | Decision |
|---|---|---|---|
| R_SENSE-set full scale + verified TPS fault latch | Minimum components | Sense short/driver faults need energy/timing evidence | **Try first** |
| Independent current threshold comparator per branch | Can react to a fault that TPS itself fails to bound | Adds 8 comparators/sense paths; common sense element may be shared-failure | Add only if proven necessary |
| Dedicated branch input cutoff/high-side switch | Can isolate an individual faulted branch | Added MOSFET/driver and branch SOA; common bus break may suffice for unsafe failures | Defer unless test fails |
| Reliance on global PSM current breaker alone | Minimal | One overloaded COB could be harmed while total bus current remains below breaker threshold | **Reject as sole COB protection** |

An upstream PSM eFuse protects the **source and shared power path**, not
necessarily one COB at ~2 A. The driver's cycle-by-cycle switch limit likewise
is **not** a 2.34 A emitter protection specification.

### 7.3 Priority fault test

Inject the sense resistor open/short and high DIM/full-scale command on a
current-limited LED emulator, not a live expensive COB at uncontrolled energy.
Measure:

- how quickly the TPS asserts FAULT;
- how quickly the independent latch forces the **physical EN/PWM pin** low;
- peak/output integrated current and transient energy, including COUT;
- whether a driver fault remains hazardous after EN is forced low;
- whether the common PSM disconnect is required.

**No automatic claim** that the internal TPS FAULT latency is fast enough
to preserve emitter ratings; if not, close the gap with hardware.

## 8. Module and thermal interlocks

LEM identity and calibration:

- CM validates descriptor version, bounded fields and CRC;
- invalid/missing descriptor keeps hardware arming false;
- no module NVM field can widen the PSM's proven electrical range;
- CRC is accidental-corruption detection, not source authentication.

Temperature:

- WW and CW passive temperature sensors required in the starting concept;
- reject open/short/implausible measurements;
- hardware-comparator cutoff is an option when ordinary CM polling plus
  independent CM-watchdog fail-off does not provide sufficient verified fault
  reaction time;
- one sensor per bank is **not** proof that one poorly mounted COB is safe;
- fan stall must be covered by tach/airflow/thermal response and quantified
  thermal time constants;
- TPS92205x junction thermal foldback does **not** substitute for LEM sensing.

An always-running external watchdog does not detect a *logical* error when
firmware continues servicing it incorrectly. Keep trust boundaries explicit;
add analog/hardware thermal comparators only where failure analysis needs them.

## 9. Preferred minimal protection blocks

```text
External qualified 48 V source
          |
     input fuse/TVS (coordinated, exact parts OPEN)
          |
  input protection controller / external FET breaker
   (UV / OV / reverse / OC / qualified inrush)
          |
    protected LED power bus ---------------------+
          |                                      |
    TPS922054 branch x8                        input monitor
          |                                      |
     LEM WW / CW                              FAULT / latch
          |
      physical LED outputs

Separate protected low-voltage safety supply
          |
   independent watchdog <----- CM health heartbeat
          |
   source / fault / thermal / presence permissives
          |
   fault latch + normally-off output-permit gate
          |
   dominates every branch EN/PWM pin
          |
   CM may request PWM/analog intensity only while armed
```

**Not a schematic.** Control and power-path signal polarity, low-voltage
supplies, fault catch conditions, power sequencing, source cutoff behavior,
grounding and PSM/LEM connector pinout are OPEN.

### Minimal first prototype bill of *functions*, not final parts

- one watchdog and appropriate reset/enable network;
- one common latch and dominant EN/PWM inhibit;
- one input protector / gate controller and rated disconnect MOSFETs;
- one qualified bus-current sense element;
- sense/network resistors and TVS/fuse / inrush and bulk coordination;
- per-branch sense resistor, normal TPS fault interface and access/testpoints;
- two bank thermal paths and LEM valid/presence permission;
- fault/status communication to CM for diagnostics/rearm.

**Do not populate 8 current comparators, 8 retry machines or a second MCU
until a measured fault case requires them.**

## 10. Assessed options and decision record

| Concern | Preferred first implementation direction | Still OPEN |
|---|---|---|
| MCU liveness | External window watchdog, e.g. TPS3430 class | Actual part, window/timeout, boot sequence |
| Driver fault | Common hardware FAULT capture and latched inhibit | Electrical logic, real pin truth table / masking |
| Branch enable | Dominant default-low gate on each EN/PWM | Actual logic/pull topology and fanout |
| Input protection | External-FET protector, e.g. LTC4368 class | Supply rating, MOSFET SOA, thresholds, fuse/TVS |
| Normal branch current | Hardware-selected R_SENSE + software DIM command | Worst-case maximum current and sensing faults |
| Exceptional branch overcurrent | Test TPS FAULT-to-inhibit first; comparator only if needed | Peak current/energy, response time |
| Source upgrade | Requalify source + protector + connector/cable + thermal | New qualified class, no automatic NVM authorization |
| Thermal | WW/CW NTCs plus watchdog-supervised CM, independent response where needed | Sensor topology, independent analog trip requirement |
| LEM descriptor | CRC/version and fail-off arm | NVM IC/bus/schema and service workflow |
| Failed-on switch | Common input isolation plus explicit residual-energy check | Demonstrated isolation under FET/control single faults |

**Selection:** follow this direction for **one protected TPS922054 test branch**
and a **separate low-energy shared-protection test fixture**, without freezing
all eight branches or connectors.

## 11. Verification gates before a Rev A multi-branch PCB

The following tests are additions/refinements to
[VT-01 through VT-17](prototype-a-power-and-fault-safety.md).

| Gate | Target | Required evidence | Failure blocks next step? |
|---|---|---|---|
| PG-01 | Power-up / brownout / watchdog rail missing | Physical EN/PWM pin and LED current remain off | **Yes** |
| PG-02 | CM heartbeat missing, stuck high, stuck low, too fast | Independent watchdog drops output permission; no retrigger loop | **Yes** |
| PG-03 | FAULT generated under still-switching condition | External latch inhibits even without CM firmware | **Yes** |
| PG-04 | FAULT clears while MCU is hung | Latched fault remains off until deliberate qualified rearm | **Yes** |
| PG-05 | Sense resistor short/open and stuck high DIM | Current peak, time, energy within emitter-safe envelope or defined blocker | **Yes** |
| PG-06 | Controlled all-branch-full-power demand on emulator | Independent input limiter disconnects unsafe demand for selected source | **Yes** |
| PG-07 | Protected input OV, UV, polarity reversal, hot plug | External FET/input protection, safe output; components remain within ratings | **Yes** |
| PG-08 | FET internally stuck-on or inhibit line ineffective | Upstream isolator and residual bulk/COUT energy; no false safe-state claim | **Yes** |
| PG-09 | Missing NVM, invalid calibration, LEM presence/NTC fault | LEM permission not granted, or verified safe derate/cutoff | **Yes** |
| PG-10 | Fan blocked / localized COB contact loss | Qualified thermal trajectory and coverage; add sensing if required | **Yes** |
| PG-11 | Normal current dimming at 24–60 fps | No newly introduced optical banding / artifacts in qualified matrix | Before release |
| PG-12 | One protected branch validated, source protector independently validated | Separate signed-off measurement records and limits | **Yes** |

Thresholds (including current, timeout, UV/OV, temperature and allowed fault
energy), device part selections and response-time budgets remain **OPEN**.
An untested gate is **NOT VERIFIED**, never accepted by assumption.

For safer prototyping, use a limited-energy load emulator and a programmable
bench supply. Never intentionally make a 600 W demand from a 400 W-class source
or perform energized LEM connector removal.

## 12. Deliberate non-goals

- No second safety MCU or distributed functional-safety framework.
- No fixed 300 W hardware comparator or wattage-based false certification.
- No simultaneous hardware design of eight identical converter branches.
- No claim of complete single-point-failure tolerance from a shared latch.
- No final connector, source, MOSFET, sense value or UV/OV threshold today.
- No reliance on PSU hiccup, branch FAULT telemetry, or driver thermal
  foldback as the sole safety policy.
- No guarantee of optical flicker performance without camera/photodiode tests.

## 13. Next exact step

Create a **protected single-branch schematic capture proposal** with:

1. actual default-off EN/PWM gate and watchdog connection;
2. real TPS922054 FAULT truth-table and latch wiring;
3. source-protection evaluation circuit, including power-path topology and
   auxiliary/fan power partition;
4. full tolerance/corner budget for ~95 milliohm R_SENSE, current-reference and
   capacitor/transient behavior;
5. controlled fault-injection/testpoints.

Review and simulate these circuits independently, then combine the one-branch
schematic. Explicitly hold the eight-branch PSM board until protection gates
have objective evidence.

## Manufacturer sources

1. Texas Instruments, TPS922052/53/54/55 datasheet SLVSGG9B Rev. B.
   https://www.ti.com/lit/ds/symlink/tps922054.pdf
2. Texas Instruments, TPS3430 window watchdog.
   https://www.ti.com/product/TPS3430
3. Texas Instruments, TPS3431 programmable watchdog.
   https://www.ti.com/product/TPS3431
4. Texas Instruments, TPS3850 window watchdog and low-voltage supply supervisor.
   https://www.ti.com/product/TPS3850
5. Analog Devices, LTC4368 100 V surge/reverse UV/OV and bidirectional circuit
   breaker controller (2.5–60 V specified operation), external FETs.
   https://www.analog.com/en/products/ltc4368.html
6. [Accepted output-power policy](../adr/0001-power-envelope-policy.md)
7. [Safety contract](prototype-a-power-and-fault-safety.md)
