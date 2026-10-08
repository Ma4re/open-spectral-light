# Prototype A — Power and Fault Safety Contract

**Status:** PRE-DECISION ENGINEERING STUDY — proposed safety contract, not an
approved protection schematic, product safety certification, or test result.

**Reviewed:** 2026-10-08  
**Scope:** Phase 1 CM ↔ PSM ↔ LEM, nominal 48 V external DC, two logical
white roles (WW/CW), four separately current-regulated COB branches per role.

This study addresses the highest-priority gaps in
[`2026-10-08-documentation-sanity-check.md`](2026-10-08-documentation-sanity-check.md).
It supplements, but does not supersede, the Phase 1 product requirements.

## 1. Executive decision

**Keep** the three-module architecture and the TPS922054/055 analytical branch
candidate. **Do not** replicate the branch eight times or release a production
PSM until the protection implementation has been selected, simulated, and
validated.

The principal issue is that **individually valid current commands can produce
an invalid total load**. Phase 1 needs:

1. a normal, accurate `P_LEM` allocation policy;
2. independent branch current ceilings and fail-off control;
3. independent protection against sustained excessive PSM input power/current;
4. a hardware disable path effective when the controller stops working;
5. explicit invalid-LEM, temperature and driver-fault behavior.

A second safety MCU and an elaborate safety bus are **not** presumed necessary.
A supervised shared-enable path, appropriately designed fault latch, sensing and
input protection are the first low-complexity hardware direction to evaluate.

**Crucial caveat:** a source overcurrent trip that protects a ~400 W PSU does
not, by itself, prove an exact independent **300 W** LED-module power ceiling.
That distinction must remain explicit until the safety analysis and response-time
measurements justify the implementation.

## 2. Source-backed facts and design assumptions

### 2.1 Prototype A electrical facts

| Quantity | Status | Value / interpretation |
|---|---|---|
| WW/CW logical roles | Architecture direction | Two roles, four branches each |
| Individual COB current | Prototype TARGET | ~2.11 A nominal high-power full scale |
| COB rated maximum | Part-dependent datasheet boundary | 2.34 A; not a usable universal hardware trip threshold |
| Typical COB high-power voltage | Engineering estimate | ~35.5 V |
| Installed nominal LED output at all branches full | Calculated *fault scenario* | 8 × 2.11 A × 35.5 V ≈ **599 W** |
| Higher-voltage conservative comparison | Calculated *not guaranteed simultaneous* | 8 × 2.11 A × 41.2 V ≈ **696 W** |
| Phase 1 total LED output | TARGET | ~300 W combined WW + CW |
| External DC source | TARGET | Regulated 48 V nominal, ~400 W class |
| PSM branch topology | Preferred study direction | Eight independent constant-current bucks |
| Prototype branch-driver candidate | TARGET, not frozen | TPS922054 first branch, TPS922055 EMI comparison |

These power figures describe **different conditions**. The 600 W and 696 W
calculations illustrate what could be demanded without aggregate control; they
are **not** a permitted operating mode, a measured input-power figure, or a
prediction of what a supply will actually deliver when overloaded.

Approximate 300 W LED operation at 94–96 % converter efficiency requires
~313–319 W at the converter inputs *before* fan, controller and other auxiliary
loads. Consequently, the actual PSU margin depends on auxiliary consumption,
efficiency, tolerance, derating and input voltage.

### 2.2 What TPS92205x protects — and what it does not

TI lists LED open/short detection, current-sense faults, FET faults, thermal
shutdown and an open-drain `FAULT` indication. Its internal cycle-by-cycle
**switch** current limit is considerably higher than the approximately 2.11 A
LED command and 2.34 A COB maximum. It is **not** a guaranteed COB-safe
continuous-current limit.

**Important Table 7-3 behavior in TI datasheet SLVSGG9B, Rev. B:**

- LED open load: device stops switching and can recover when the detection
  condition clears.
- LED+ to LED− short: `FAULT` asserts; **device keeps switching with minimum
  on-time**.
- Sense-resistor short: `FAULT` asserts; **device keeps switching under
  cycle-by-cycle current limiting**.
- Thermal shutdown: device stops switching but can automatically recover after
  cooling.

Thus, merely exposing `FAULT` to controller telemetry is insufficient.
The PSM shall evaluate a **hardware-capable shared inhibit/latch path** so a
fault requiring lockout remains off even if the TPS itself keeps switching or
auto-recovers. No particular latch IC or polarity network is frozen yet.

TI specifies that `EN/PWM` low disables the device; its `ADIM/HD` pin also
selects dimming modes. **Never assume pulling `ADIM/HD` low alone makes the
branch safe.** The safe output-off path must control actual enable/energy
delivery and be tested through controller reset and disconnected signals.

TI's `TEMP` thermal foldback senses **driver junction behavior**, not the
individual COB thermal interfaces. It supplements but never replaces LEM
temperature sensing.

## 3. Responsibility and trust boundaries

| Function | Controller Module (CM) | Power Stage Module (PSM) | Light Engine Module (LEM) |
|---|---|---|---|
| Requested CCT/brightness | Owns | Executes bounded request | No control authority |
| Continuous module power allocation | Owns normal policy | Enforces compatible requests and independent overload response | Reports rated envelope |
| Per-branch regulated current | Requests logical bank behavior | Owns branch circuit, setpoint ceiling, fault response | Presents qualified loads |
| Source input safety | Observes diagnostics | Owns input protection, undervoltage/overvoltage, overload response | Not responsible |
| Safe enable | Must deliberately arm, provide freshness | Default-off and independently revocable enable | Presence/interlock information |
| COB temperature | Applies derating policy | Able to inhibit on thermal fault | Passive temperature path(s), characterized thermal limits |
| Device thermal safety | Receives status | Independent driver protection and selected board limits | Thermal path to head |
| Module identity | Parses compatible descriptor | May require CM arm, but must not accept NVM as authority to exceed PSM hardware | Versioned NVM identity and calibration |
| Fault indication/recovery | Displays/logs, deliberate rearm | Detects, inhibits/latches according to severity | No automatic override |

**Trust model:** normal CM firmware may compute accurate LED power, but a
firmware hang, incorrect duty output, corrupted command or stale state must not
be able to sustain an uncontrolled high-power state indefinitely.

Likewise, LEM NVM must only **constrain** an independently safe PSM envelope;
its contents do not become a hardware-authoritative protection threshold.

## 4. Proposed power-envelope hierarchy

### 4.1 Distinct limits

| Name | Definition | Enforcement direction |
|---|---|---|
| `I_BRANCH_OPERATIONAL_MAX` | Normal per-COB command ceiling (~2.11 A target) | Configured branch setpoint and CM allocation |
| `I_BRANCH_COMPONENT_MAX` | Emitter datasheet absolute/current maximum (2.34 A for candidate V18) | Must be respected over tolerances/transients; not synonymous with IC switch OCP |
| `P_LEM_NORMAL_MAX` | Total permitted operating LED electrical output (~300 W TARGET) | CM allocation, PSM validation; required normal behavior |
| `P_LEM_FAULT_ENVELOPE` | Maximum allowed energy/time excursion beyond normal operating point | **OPEN**; select from emitter/thermal and hazard analysis |
| `P_PSM_INPUT_ALLOWED` | Actual allowed input power/current across source voltage, including auxiliary loads | Input supervisor/limit/disable and source coordination |
| `V_BUS_FULL_POWER_MIN` | Candidate input where all qualified operating points can run | ~46 V initial study target, **OPEN** |
| `V_BUS_MAX_SAFE` | Source tolerance plus protected transients consistent with driver ratings | **OPEN**, must distinguish recommended operating from absolute maximum |

A normal firmware limiter can implement **exact** total output-power
allocation. An independent simpler input current/power supervisor can bound
a **hazardous sustained overload** without necessarily clamping exactly at
300 W. A true hardware-enforced 300 W constant-power limit would require a
separate demonstrable output-power constraint (or a proven conservative
current/voltage envelope), not merely a statement about the input fuse.

### 4.2 Normal operating calculation

At every control update, the CM shall derive calibrated branch current
requests from requested brightness and CCT and apply a total LED power budget:

```text
P_LEM_est = sum over enabled branches (V_LED_branch_est × I_LED_branch_cmd)
P_LEM_est <= min(P_LEM_normal_target, LEM_declared,
                 PSM_qualified, thermal_derated, available_input_budget)
```

This estimate requires a defined treatment of forward-voltage uncertainty,
calibration validity and current-setting tolerance. Initial conservative
estimation is acceptable. Final current/output telemetry can refine it, but
is not necessary to invent a complex distributed data bus now.

The allocation shall behave sensibly across the WW/CW mix, including both
banks active concurrently. On reaching a budget, lower the commanded output
without intentionally alternating WW/CW in time to synthesize color.

### 4.3 Independent hazardous-overload containment

Preferred low-complexity direction to evaluate:

```text
Regulated external 48 V
       |
  input reverse/OVP/inrush protection
       |
  shared input measurement / overload decision  --+ 
       |                                         |
  supervised PSM power enable <---- hardware inhibit/latch
       |
  eight separately regulated branches
       |
  LEM (4 WW + 4 CW)
```

An input-sense comparator, suitably rated power limiter/eFuse, or analogous
hardware may provide the independent overload decision, **subject to
measurement and response-time analysis**. The external AC/DC supply's own
overcurrent/hiccup is a last-resort feature, not the sole system safety policy.

A fault of one supply component, current monitor or shared-enable signal
must be considered explicitly. No unverified claim of single-fault tolerance
is made here.

**Do not freeze:** an 8.33 A threshold, a 300 W constant-power comparator, a
fuse rating, supervisor IC, or a protection delay from these approximate
figures. They depend on accepted source derating, actual 48 V input envelope,
driver efficiency, thermal time constants and stored energy.

## 5. Default-off and arming contract

### 5.1 Logical states

```text
UNPOWERED
    |
    v
SAFE_OFF / DIAGNOSTIC
    |
    | source valid + module presence + NVM validated
    | sensors valid + supervisor valid + deliberate arm
    v
ARMED (outputs still off)
    |
    | bounded setpoints + safe enable
    v
EMITTING <--> DERATING
    |
    | hazardous fault
    v
FAULT_LATCHED -> SAFE_OFF after explicit safe reset/rearm
```

`SERVICE_LOCKOUT` is a SAFE_OFF cause for incompatible or unverified LEM
metadata. The bench may support an **explicitly separate, current-limited**
diagnostic mode; no normal light output is allowed under that exception.

### 5.2 Enabling invariants

1. Power-up, brownout, reset, MCU bootloader entry and unconnected controls
   must **not** result in LED output.
2. A single static logic level stuck in the enabled state must not be accepted
   as proof of a healthy controller. Evaluate a watchdog/freshness mechanism
   independent of normal application scheduling; the exact mechanism is OPEN.
3. Only after module identity, compatibility and sensor plausibility checks may
   the CM deliberately arm the PSM.
4. Current commands shall be initialized to a safe zero, bounded, and then
   ramped deliberately under the current/power limits.
5. A latched hazardous fault requires a defined deliberate reset/rearm
   sequence, never an uncontrolled power oscillation.
6. Disabling switching does **not** prove instantaneous LED current becomes
   zero: output capacitors and stored inductive energy need measurement and
   safe discharge/settling characterization.
7. Phase 1 module replacement remains a **powered-off service operation**.

### 5.3 Proposed hardware gating concept (not a schematic)

```text
Controller ARM + independent freshness condition
              |
LEM present / valid-state permissive*
              |
source-voltage & input-overload permissive
              |
temperature-permissive / fault latch
              |
              v
         PSM GLOBAL_INHIBIT  ----------> all branch EN/PWM paths
                          (default forces OFF)
```

`*` Identity validation is performed by CM firmware; do not represent this
software result as cryptographically authenticated or physically independent
without additional hardware. Physical module presence and default-off controls
can be independently enforced with a simple dedicated signal if justified.

The eight open-drain FAULT outputs may be collected into a single PSM fault
signal and/or latch, **after** checking the actual fault output truth table,
pull-up voltages, leakage, wire break detection, startup transients and
interaction with EN/PWM. Any fault that might continue switching must not rely
on firmware polling for its sole shutdown.

Using a simple common shutdown first is preferred to implementing eight
independent sophisticated retry state machines.

## 6. NVM / calibration safety contract

The LEM's nonvolatile descriptor shall minimally identify:

- supported major/minor interface profile and compatible branch topology;
- module type/revision, unique identity and calibration format version;
- qualified maximum per-branch current and allowed total module power;
- defined thermal sensor type/meaning and qualified limits;
- calibration state (not calibrated / calibrated / invalid);
- an integrity check and validation rules for bounded fields.

For this prototype, a CRC (or equivalent integrity check) is appropriate to
detect accidental corruption; it **does not authenticate** a maliciously edited
module. Signatures, provisioning infrastructure and security keys are not
requirements of a simple powered-off replaceable studio light without a defined
threat model.

**Safe acceptance rule:**

```text
effective_limit = min(physical_PSM_qualified_limit,
                      validated_LEM_declared_limit,
                      applicable_thermal_limit,
                      product_operational_limit)
```

Never use NVM contents to enlarge PSM circuit capability. Missing identity,
bad CRC, incompatible version, nonsensical parameters, invalid temperature
metadata and unknown calibration state all inhibit **normal** high-power output.
A dedicated service characterization state must be explicit and physically
limited rather than a silent fallback to full power.

## 7. Thermal safety contract

Protection is layered:

1. **COB thermal path:** LEM heat spreader, TIM, attachment and mechanical
   design shall be qualified at worst allowable continuous power.
2. **Bank sensors:** a passive/analog temperature path near representative WW
   and CW sites detects bank-scale asymmetric loading; open and short are
   invalid unless separately characterized as meaningful readings.
3. **Coverage gap:** one WW and one CW temperature reading may miss one
   detached/mis-mounted COB. Analyze that failure mode and qualify assembly
   process/contact coverage, additional sensing if justified, or a reduced
   electrical power envelope.
4. **Fan:** failure, stall or unavailable airflow must result in qualified
   derating or off before temperatures exceed accepted limits.
5. **Driver:** TPS92205x internal junction foldback/thermal shutdown protects
   the driver locally, not the remotely attached LED junction/case.

Exact COB case trip temperatures and derating curves remain OPEN and must be
derived from Bridgelux data, TIM geometry, ambient, tolerances and thermal tests.
The 105 °C published maximum case rating must not become the routine operating
target.

## 8. Fault-response matrix

**Severity levels**

- **S0:** normal bounded operation / informational only.
- **S1:** controlled power reduction while readings remain trustworthy.
- **S2:** inhibit all emission; require explicit rearm after fault clears.
- **S3:** hard/inhibited output, service/inspection before rearm; source isolation
  as required if a branch cannot be de-energized by normal enable.

These levels are proposed policies, not validated thresholds or time budgets.

| ID | Stimulus / failure | Detection / evidence required | Minimum desired response | Open design issue |
|---|---|---|---|---|
| FS-01 | All 8 branches commanded maximum (~600 W nominal installed demand) | Normal `P_LEM` budget; independently monitored input overload | S1 normal clamping; S2 if demand persists outside safe envelope | Distinguish 300 W normal cap from hardware overload trip |
| FS-02 | One branch command exceeds rated/qualified COB current | Branch setpoint bounding; current sense and fault observation | S2 branch or global inhibit before damaging excursion | Independent branch overcurrent limit and response time |
| FS-03 | Current-sense resistor open | TPS FAULT plus independent disable path | S2 latched | Validate TPS detection timing and analog maximum excursion |
| FS-04 | Current-sense resistor short | TPS FAULT **while switching may continue** | S2 external latch/global inhibit | Ensure FAULT actually removes drive despite IC auto-behavior |
| FS-05 | LED terminals short | TPS FAULT **while minimum-on-time switching may continue** | S2 external latch/global inhibit | Fault current, energy, MOSFET/diode stress |
| FS-06 | LED open / connector break under load | TPS FAULT, LED voltage observation | S2 inhibit; no automatic hot replug | Stored energy, open-load output, reconnection transient |
| FS-07 | Switching FET internal fault or driver IC failure | FAULT if available; input/branch safety observability | S3 if EN/PWM cannot interrupt output | Physical means of isolating a failed-on power stage |
| FS-08 | MCU reboot, watchdog reset, halted control loop | Independent enable/freshness supervision | S2 default off; controlled rearm | Hardware timeout/independence |
| FS-09 | Controller output pin stuck high; runaway PWM/dimming | Bounded setpoint plus independent enable/overload mechanism | S2 on unsafe sustained command | Prove stuck-on command cannot bypass protection |
| FS-10 | Module detached/replaced while powered | No hot-swap; mechanical/service policy and module-presence behavior | S2/S3, no live rearm | Connector arcing, stored energy and interlock need |
| FS-11 | Missing/incompatible/bad-CRC LEM NVM | Descriptor validation; independent PSM limits | S2 normal emission inhibited | Boot-time trusted-safe mode |
| FS-12 | WW or CW NTC open/short | Range/plausibility checks; analog fail path as applicable | S2; no uncontrolled full output | Sensor network and hardware fault detection |
| FS-13 | One COB poor thermal contact | Local-case validation; fault-injection/thermal analysis | S1 or S2 before damage | 2 bank sensors may be insufficient |
| FS-14 | Fan stalled, airflow blocked, fan supply failed | Tach/airflow or validated thermal behavior | S1 then S2, or direct S2 if safe margin unknown | Time-to-limit and sensor coverage |
| FS-15 | Source overcurrent / overload / PSU hiccup | Input sensing and source status if available | S2 bounded controlled loss of output; no uncontrolled reboot cycling | Input protection element and latch policy |
| FS-16 | 48 V bus sag below validated full-power limit | Input voltage measurement independent of LED regulator | S1 qualified derating or S2 | Brownout timing and dropout with cold COB |
| FS-17 | Bus overvoltage or connection transient | Input OVP/clamp and component ratings | S2/S3 | TVS/eFuse/PSU tolerance and clamp coordination |
| FS-18 | TPS FAULT due to local thermal shutdown | Driver FAULT and board temperature | S2; prevent uncontrolled auto-restart | TI automatic thermal recovery, latch |
| FS-19 | A branch disabled while others remain active | Per-branch status; optical and power telemetry | S1 or S2 depending on chromaticity/uniformity | A four-COB bank missing one emitter may generate unsafe color pattern |
| FS-20 | Incorrect WW/CW calibration or commanded powers | NVM integrity, bounded output, plausibility checks | S1/S2; prohibit unvalidated full-scale calibration | Avoid exposure to unbounded commissioning mode |
| FS-21 | Control/temperature/fault wire open or short | Defined electrical pulls and diagnostic coverage | S2 for safety-relevant lines | Floating/open line behavior by connector |
| FS-22 | Common hardware inhibit stuck ineffective | Isolation/overload device and proof testing | S3 | Common-cause failure; no unsupported single-fault claim |

**No automatic retry** should be assumed for S2/S3 faults merely because the
TPS92205x itself can recover. Fault classification, latching, and rearm rules
must be verified, not just described in the app UI.

## 9. First implementation candidates: minimum hardware

Evaluate the following in this order to avoid overengineering:

### A. Mandatory common controls

- One **default-off common output inhibit** distributed to all eight drivers.
- An independent controller-alive/freshness condition controlling arm.
- A shared fault latch / inhibit that reacts without waiting for application
  task scheduling, especially for Table 7-3 continue-switching cases.
- Dedicated test points allowing measurement of actual EN/PWM, FAULT, ADIM/HD,
  VIN, branch current and output voltage.

### B. Input-side protection

- Source-compatible reverse-polarity/OVP/inrush/protection design.
- Independently evaluated overcurrent/power event response at the **entire**
  PSM input, including auxiliary load and capacitor inrush.
- Defined undervoltage/dropout/derating behavior using the real PSU limits and
  protection losses.

**Do not equate a simple input fuse or the PSU's hiccup response with a
regulated 300 W LED output budget.**

### C. Per-branch current safety

- TPS922054/055 RSENSE and commanded duty/current limits.
- Fail-safe command electrical defaults with bounded reference behavior.
- Evaluate whether a separate current-comparator/protection path is needed for
  the COB's 2.34 A maximum or whether the combination of hardware setpoint
  limit, FAULT latch and measured transients is sufficient.
- If any single-point failure could allow damaging COB current despite those
  provisions, document that risk and redesign the branch protection before
  considering production hardware.

### D. Thermal sensing

- At least WW/CW independent bank-temperature paths, explicitly tested for
  open/short.
- No claim of full individual-COB protection from two sensors alone.

### E. Deliberately excluded from first prototype

- A second generic safety MCU.
- A proprietary multi-drop control/safety bus.
- Eight independent auto-restart frameworks.
- Authentication/signatures for replaceable-module calibration without a
  relevant threat model.
- Live hot-swappable LED connectors.

If the simple design fails a measured safety requirement, add **only the
specific hardware necessary** to close that failure mode.

## 10. Required verification and acceptance evidence

### 10.1 Test infrastructure

- Current-limited bench DC source and separately qualified fault-limited
  injection loads.
- Adjustable electronic LED emulator/load, or real COB at safe controlled
  power when needed.
- Oscilloscope probes rated for common-mode and switching-node voltage.
- Instrumented `V_BUS`, `I_BUS`, `V_LED[n]`, `I_LED[n]`, fault and enable signals.
- Thermal sensing with representative COB case/cold plate and PSM PCB
  measurements.
- Means to remove controller drive without removing PSM power.
- Ability to command a **simulated** eight-branch load without physically
  delivering ~600 W into a 400 W bench supply.

### 10.2 Verification matrix and pass criteria

| Test | Requirement / injection | Evidence to record | Pass criterion |
|---|---|---|---|
| VT-01 | Power-up, MCU boot/reset and controls disconnected | EN/PWM, branch current, bus waveform | No unintended sustained emission; validated startup transient envelope |
| VT-02 | LEM missing, wrong interface version, invalid CRC, uncalibrated | Module ID result, arm signal, LED output | Normal emission inhibited |
| VT-03 | Normal WW/CW CCT sweep including intermediate mixing | Branch V/I, computed total LED watts | Never sustains a command above the qualified normal aggregate budget |
| VT-04 | Inject all-eight-full-scale demand via safe emulator | Input/budget status, latch and output currents | Input and LED energy bounded by qualified time/power envelope; no PSU uncontrolled cycling |
| VT-05 | Branch setpoint high, stuck ADIM signal and current-sense short/open | Actual branch current and FAULT, disable latency | No damaging COB overcurrent; fail safely independent of firmware |
| VT-06 | LED short/open, hot/reconnect forbidden test via emulator | Switching/FAULT, current/voltage, discharge | Safe limit/inhibit; defined no-hot-reconnect behavior |
| VT-07 | Force driver FAULT conditions documented as continuing switching | FAULT and EN/PWM/branch I waveforms | External inhibit stops hazardous drive despite IC internal behavior |
| VT-08 | MCU hang; static ARM stuck high | Hardware freshness output, enable, current | Output disabled without firmware assistance |
| VT-09 | NTC open/short, one bank hot, fan failed | Sensor data, hardware gate, temperature trajectory | Qualified safe derate/off before defined thermal limit |
| VT-10 | Source sag, hot plug, overvoltage/inrush (instrumented emulation) | Voltage/current, protector action | No ratings exceeded; safe rearm/recovery |
| VT-11 | FAULT clears automatically inside driver | Latch state, output, rearm event | No uncontrolled automatic restart of an S2/S3 fault |
| VT-12 | Shared inhibit stuck/failed; physically forced branch fault | Residual LED current and source isolation response | Documented independent containment or explicitly unresolved hazard |
| VT-13 | Normal dimming and optical validation after adding protectors | LED optical waveform, CCT/Duv, EMI | Added protections do not invalidate normal-video temporal acceptance |

**Acceptance is not defined by nominal labels alone.** For tests VT-04,
VT-05, VT-07, VT-08, VT-09 and VT-10, the reviewer must approve actual
thresholds, response times, transient energy, component tolerances and
measurement uncertainty *before* claiming PASS. Unknown results are
**NOT VERIFIED**, never implicitly green.

### 10.3 One branch before eight

First validate with **one** TPS922054 branch, including EN/PWM fail-off,
current-sense fault, LED short/open, FAULT and local thermal behavior.

Next validate the **shared supervisor** with representative dummy loads, so
the aggregate fault can be tested without building the full high-power engine.
Only then replicate eight power stages.

## 11. Fault-to-verification traceability

The `FS-xx` entries above define **required behavior** and `VT-xx` entries
define proposed evidence. A passing branch-level test does not necessarily
close a head-level fault: note which hardware boundary is actually exercised.

| Fault IDs | Required tests | Verification level / gap |
|---|---|---|
| FS-01, FS-15 | VT-03, VT-04, VT-10 | CM policy plus independent aggregate-load supervisor; one LED branch cannot prove eight-branch overload safety |
| FS-02, FS-03, FS-04 | VT-05, VT-07 | Branch plus hardware inhibit; test faults where the TPS continues switching |
| FS-05, FS-06 | VT-06, VT-07 | Branch and safe load emulator; never test unplanned live reconnect on a real COB |
| FS-07, FS-22 | VT-12 | Forced ineffective enable / stuck-switch scenario; source isolation must be demonstrated or remain an unresolved design blocker |
| FS-08, FS-09 | VT-01, VT-05, VT-08 | Independent freshness, static stuck-on controls, reset and lost communications |
| FS-10, FS-11 | VT-02, VT-14 | Safe boot and emulated module-presence loss; **no energized LEM unplug test** |
| FS-12, FS-13, FS-14 | VT-09, VT-15 | Thermal fault injection; specifically account for one COB with poor contact not seen by bank sensors |
| FS-16, FS-17 | VT-10 | Voltage/input-protection corners and rated transient energy |
| FS-18 | VT-07, VT-11 | Fault and thermal autorecovery versus external latch/rearm |
| FS-19 | VT-13, VT-16 | Loss of a physical COB branch while the rest of its logical bank is still active |
| FS-20 | VT-02, VT-03 | Wrong but CRC-valid calibration values, bounds and plausible power allocation |
| FS-21 | VT-01, VT-09, VT-17 | Open/short control, NTC, enable and FAULT wiring; verify default electrical states |

Add these explicit verification cases to the preceding matrix:

| Test | Requirement / injection | Evidence to record | Pass criterion |
|---|---|---|---|
| VT-14 | Emulate loss of LEM presence during enabled operation **without physically opening a powered connector** | Presence logic, enable, LED current and rearm behavior | Emission safely inhibited, no automatic return on restored presence |
| VT-15 | Emulate an individual COB with an abnormal thermal interface using a safe limited-energy fixture or validated thermal model | Local case/hotspot and bank-sensor temperatures versus current | Coverage demonstrably adequate **or** document a blocking gap and introduce a corrective measure |
| VT-16 | Inhibit one WW or CW branch while other branches operate | Remaining branch currents, output uniformity and chromaticity | No unsafe redistribution; qualified degraded mode or controlled all-off behavior |
| VT-17 | Disconnect/short each safety-relevant control and diagnostic line individually, using a protected fixture | Physical enable and fault states, current and response time | Defined default-off or diagnostically safe condition; otherwise explicit unresolved blocker |

**Traceability status:** these are *planned tests*, not evidence of passing
hardware. Actual pass/fail and waveform references belong with the executed
branch and head-level test records.

## 12. Pre-Rev-A decisions still OPEN

1. Whether the normal 300 W LED ceiling is also required as an **independently
   guaranteed exact hard limit**, or whether normal accurate 300 W allocation
   plus a separately qualified higher/short-duration hardware fault limit meets
   the thermal/safety requirement.
2. Actual source continuous and transient envelope (input voltage tolerance,
   derating, maximum current, connector and cable drops).
3. PSM shared overload response: comparator/limiter/eFuse/fault latch and
   verified response time.
4. Branch setpoint bounding, current-sense short/open fault response and
   guaranteed worst-case emitter current/transient energy.
5. Independent freshness supervisor or other non-static proof of continued
   controller supervision.
6. Branch FAULT wired behavior, latch/reset semantics and power isolation
   under a stuck switching device.
7. NTC values and coverage of individual-COB thermal-interface faults.
8. Fan fault sensing and conservative fallback behavior.
9. LEM NVM format/version/integrity and commissioning state.
10. Full voltage/current/thermal tolerance and FMEA evidence.

These are implementation choices requiring further investigation, not reasons
to select a different MCU or LED driver today.

## 13. Decision gate and next exact step

**This study completes the logical CM–PSM–LEM safety contract.**

The next engineering artifact should be a **minimal protection-architecture
comparison** for:

- default-off supervised enable with independent freshness;
- fault aggregation/latch for TPS922054's continue-switching cases;
- bounded branch setpoint/current under single faults;
- independent PSM input-overload shutdown/limiting;
- safe response to LEM temperature/identity invalidity.

Select the smallest circuit that actually passes the fault matrix and then
apply it to the **one-branch** Rev A schematic and vendor-model simulation.
Do **not** make an eight-channel board as the first hardware validation.

## Primary references

1. Texas Instruments, **TPS922052/TPS922053/TPS922054/TPS922055
   datasheet, SLVSGG9B Rev. B**, especially sections 5, 6 and 7.3.7
   (Table 7-3 protection behavior).
   https://www.ti.com/lit/ds/symlink/tps922054.pdf
2. Bridgelux, **Gen 7 V18 Thrive Array DS322**, for the selected COB
   electrical/thermal envelope. Verify exact purchased part/bins against
   the matching current datasheet before setting protection thresholds.
   https://www.bridgelux.com/thrive
3. OpenSpectralLight, **Phase 1 tunable-white requirements**.
   ../requirements/phase-1-tunable-white.md
4. OpenSpectralLight, **Light Engine Module Interface Study**.
   light-engine-module-interface.md
5. OpenSpectralLight, **Prototype A TPS92205x Branch Design**.
   prototype-a-tps92205x-branch.md
