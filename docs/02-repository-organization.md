# Repository Organization

## Top-level domains

| Path | Responsibility |
|---|---|
| `firmware/` | Embedded product firmware and host-testable embedded logic. |
| `hardware/` | PCB/electrical artifacts organized by physical module and revision. |
| `optics/` | Mixing, diffusion, beam shaping, optical fixtures, and simulation/measurement assets. |
| `mechanical/` | Enclosure, cooling, mounts, and mechanical CAD. |
| `software/` | Optional user/host applications and calibration software. |
| `tools/` | Developer utilities that are not shipped as product software. |
| `tests/` | System, characterization, and HIL assets spanning product modules. |
| `docs/` | Architecture, standards, source-backed theory, ADRs, project policy, and roadmap. |

## Naming

Repository paths use lowercase `kebab-case` unless a tool imposes another
convention. Do not use spaces or create alternate names for the same module.
C++ source files use `snake_case.hpp` / `snake_case.cpp` as defined by the coding
standard.

## Firmware shape

The controller firmware is expected to grow toward these responsibilities only as
real code requires them:

```text
firmware/controller/
  App/
  Interfaces/
  Drivers/
  BSP/
  Tests/
  docs/
```

Do not create empty layers merely for symmetry. `Middleware/`, an RTOS service
layer, or a shared library is introduced only when concrete behavior needs it.

## Hardware shape

Hardware is organized by responsibility and then by physical revision:

```text
hardware/power/
  README.md
  rev-a/
    README.md
    kicad/
    bom/
    manufacturing/
```

Revision directories appear when an actual reviewed design exists. Temporary CAD
exports and editor/session files are not versioned as design artifacts.

## Shared code

Do not create `common/`, `shared/`, `utils/`, or similar runtime dumping grounds.
Keep behavior local to its first real owner. Extract shared runtime code only
after at least two real consumers need the same contract and implementation.
Shared data formats or test vectors may be documented before runtime sharing when
that is necessary for interoperability.

## Documentation ownership

- Root numbered documents describe cross-system architecture, engineering standards, and project policy.
- `docs/theory/` contains source-backed scientific background that explains physical principles and their design implications without freezing implementation choices.
- Module README/docs describe module-specific implementation facts.
- ADRs capture durable choices and their trade-offs.
- `agent-handoff.md` captures current work state and must not become permanent architecture.
