# Coding Standards

These rules apply to project-owned embedded firmware. They intentionally keep the
strong parts of the Sentinel Core discipline while removing security, FOTA,
networking, and platform constraints that do not belong in this project.

## 1. Simplicity first

When two implementations satisfy the same functional, safety, timing, hardware,
and architectural requirements, choose the simpler one.

- Less code wins when behavior remains equivalent.
- Concrete beats abstract until a second implementation or a real test seam exists.
- No "in case we need it later" code, configuration, or folders.
- Prefer a free function over a class when no state is needed.
- Prefer a plain `switch` over a state-machine framework while it remains clear.
- Prefer direct calls over generic event buses when ownership and timing permit.
- Remove proven-unused code; Git preserves history.
- A feature should normally be understandable from a small number of focused files.

## 2. Language and target restrictions

- Project-owned production firmware uses **C++20**.
- Target firmware uses no C++ exceptions, RTTI, or thread-safe local-static initialization.
- Project-owned target code does not use dynamic allocation unless an explicit architecture decision introduces it for a justified subsystem.
- Prefer fixed-capacity/value types: `std::array`, `std::span`, `std::string_view`, and fixed-size structs.
- Dynamic standard containers (`std::vector`, `std::string`, maps, lists, etc.) are not used in target firmware by default.
- Host-only software and tests are not forced to follow target memory restrictions when that would add no value.

Target compile restrictions must be applied to firmware targets, not globally to
host tools or third-party dependencies.

## 3. Naming

| Kind | Convention | Example |
|---|---|---|
| Types | `UpperCamelCase` | `LightController` |
| Functions/methods | `lowerCamelCase` | `setTargetCct` |
| Variables/parameters | `lower_snake_case` | `target_cct_k` |
| Private members | trailing underscore | `state_` |
| Constants | `kUpperCamelCase` | `kMaxChannels` |
| Macros | `SCREAMING_SNAKE_CASE` | `OSL_ASSERT(...)` |
| Namespaces | lowercase snake case, root `osl::` | `osl::app` |
| Files | `snake_case.hpp/.cpp` | `light_controller.cpp` |
| Pure interfaces | `I` prefix | `ILedDriver` |

## 4. Ownership and lifetimes

- Default to value semantics and `const`.
- Raw pointers never express ownership.
- References represent required non-owning access; pointers represent optional/nullable non-owning access.
- Non-owning views such as `span` and `string_view` never outlive their backing storage.
- Runtime-owned objects are built explicitly from a composition root in deterministic order.
- Non-trivial global constructors and hidden ownership are forbidden in target firmware by default.
- Recursion and variable-length arrays are forbidden unless explicitly justified and bounded.

## 5. Error handling

- Predicates return `bool`.
- Commands return a strong error/status enum when callers need a reason.
- Queries that need both data and a failure reason return a small explicit result struct.
- `assert` is for programmer invariants, not expected external/hardware failures.
- Hardware/vendor failures are normalized at the driver/BSP boundary before reaching portable application logic.

## 6. Layer rules

### App

Owns portable behavior, state, control algorithms, power/thermal policy, and color
math. It must not include MCU registers, vendor HAL, RTOS, board-pin, or concrete
peripheral headers.

### Interfaces

Contains only hardware/service seams that materially improve isolation or
host testing. Do not create one interface per class by habit.

### Drivers

Own external IC protocols, register-level device logic, and vendor callback
normalization. Drivers do not decide product policy.

### BSP

Own MCU startup, clocks, pins, interrupts, ADC/timers, DMA, board-specific
configuration, and vendor HAL integration.

## 7. Hardware and generated code

- Register access stays in BSP/Drivers.
- Pin assignments, clocks, timers, ADC scaling, current limits, protection
  thresholds, and connector pinouts are hardware contracts and must not be guessed.
- Generated/vendor code is not patched casually; project-owned extension points
  and adapters are preferred.
- A change that would disappear on regeneration is incomplete unless its
  regeneration strategy is documented.

## 8. Concurrency and callbacks

No RTOS architecture is assumed today. If concurrency is introduced:

- shared mutable state must use explicit synchronization/ownership;
- `volatile` is not a synchronization primitive;
- ISRs/callbacks do bounded work and never execute product policy;
- queue/event overflow behavior is explicit;
- task priorities/timing rationale are documented centrally before ad-hoc tasks proliferate.

## 9. Serialization and BLE-facing data

- External formats use explicit encoding/decoding; never cast raw byte buffers into structs.
- Widths, signedness, endianness, bounds, and versioning are explicit.
- Decoders validate size/range before reading fields and reject malformed input.
- BLE is a transport boundary, not a reason to leak vendor GATT types into App.

## 10. Comments and documentation

- Prefer expressive names over comments.
- Comments explain non-obvious *why*, not obvious *what*.
- Commented-out code is deleted.
- Public `Interfaces` contracts and non-obvious public `App` types are documented.
- TODOs must be actionable and referenced; vague permanent TODOs are not accepted.

## 11. Tests

- New portable behavior is test-first: RED, minimal GREEN, then refactor.
- Every public `App` behavior has deterministic host coverage appropriate to its risk.
- Tests do not sleep, use wall-clock timing, require Internet, or depend on a developer-specific path.
- A test checks one observable behavior; negative/error paths are covered when they affect output, power, thermal protection, persistence, or user control.
- Host tests never claim to prove physical optical/electrical/thermal/RF behavior.

## 12. Patterns to avoid by default

- Singletons and service locators.
- Generic event buses without a demonstrated need.
- Template metaprogramming used as architecture.
- CRTP without a measured reason.
- Framework state machines for small readable state.
- Premature plugin systems or configuration registries.

## Review questions

Reviewers should ask: did this change add hidden allocation or ownership, violate a
layer, invent a hardware fact, add speculative abstraction, leave an error path
implicit, introduce unsafe concurrency, or claim physical behavior without the
right evidence?
