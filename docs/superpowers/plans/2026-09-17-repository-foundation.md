# Repository Foundation Implementation Plan

> **For agentic workers:** implement this plan as one foundation slice and verify the resulting tree before claiming completion.

**Goal:** Establish a professional, MCU-neutral OpenSpectralLight repository scaffold with explicit architecture, coding/testing rules, and reproducible host CI.

**Architecture:** The repository is a responsibility-based monorepo. Embedded application logic will remain portable while physical electronics, optics, mechanics, optional host software, and HIL assets evolve independently behind documented boundaries.

**Tech Stack:** C++20 policy for future firmware, CMake 3.25+, CMake Presets, Ninja, CTest, GitHub Actions, clang-format, clang-tidy.

**Spec:** `docs/superpowers/specs/2026-09-17-repository-foundation-design.md`

## Global constraints

- No MCU, LED driver, spectral sensor, or emitter family is frozen in this slice.
- No OTA/FOTA, Wi-Fi, cloud, or Internet dependency is introduced.
- BLE remains a local optional control transport and no BLE implementation is selected.
- Do not create empty source layers or shared/common runtime code.
- The foundation must configure, build, and run CTest from the documented host preset.

---

### Task 1: Repository contracts and module map

**Files:** root README/contribution/agent files, `docs/`, and domain README files.

- [x] Define the project purpose, explicit non-goals, and capability tiers.
- [x] Define responsibility-based module boundaries and dependency direction.
- [x] Define repository naming/revision rules and no-premature-shared-code rule.
- [x] Adapt the proven coding/testing discipline without Sentinel-specific features.
- [x] Define color-science terminology and measurement-claim policy.

### Task 2: Neutral build and quality scaffold

**Files:** `CMakeLists.txt`, `CMakePresets.json`, `.clang-format`, `.clang-tidy`, `.gitignore`, `.gitattributes`, `.github/workflows/ci.yml`.

- [x] Add MCU-neutral C/C++ project configuration.
- [x] Add host debug/release presets without production targets.
- [x] Add formatting/static-analysis configuration for future project-owned firmware.
- [x] Add CI that configures, builds, and runs CTest on the host scaffold.

### Task 3: Verification

- [x] Run `cmake --preset host-debug` and require successful configure.
- [x] Run `cmake --build --preset host-debug` and require success.
- [x] Run `ctest --preset host-debug --output-on-failure` and require success.
- [x] Inspect the final tree for accidental MCU/driver/sensor commitments and forbidden OTA/FOTA/cloud implementation paths.
