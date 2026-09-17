# Theory Foundations Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Add three source-backed theory chapters that establish the optical and color-science vocabulary needed before component selection.

**Architecture:** Keep scientific background under `docs/theory/` and preserve `docs/05-color-science.md` as the project policy for user-visible metrics and measurement claims. The theory chapters explain physical/colorimetric principles and end with project-specific design implications without freezing hardware.

**Tech Stack:** GitHub Markdown with LaTeX math; primary references from CIE, NIST, and IES.

**Spec:** Conversation-approved documentation map for OpenSpectralLight theoretical foundations.

## Global Constraints

- Do not freeze an MCU, LED driver, sensor, emitter, protocol, or mechanical interface.
- Keep theory distinct from implementation decisions and measurement-claim policy.
- Prefer standards, metrology institutes, and peer-reviewed/official technical sources.
- Every chapter must end with `## Design implications` and `## References`.
- Preserve KISS/YAGNI: explain only theory that informs requirements, component selection, calibration, or verification.

---

### Task 1: Add the first three theory chapters

**Files:**
- Create: `docs/theory/radiometry-photometry.md`
- Create: `docs/theory/spectral-power-distribution.md`
- Create: `docs/theory/color-science.md`

**Interfaces:**
- Consumes: terminology/policy in `docs/05-color-science.md` and current system architecture.
- Produces: referenced scientific foundation for future LED, optics, sensing, calibration, and camera-related decisions.

- [ ] **Step 1: Write `radiometry-photometry.md`**

Define radiometric versus photometric quantities, spectral weighting, geometry laws, limitations, and implications for OpenSpectralLight. Cite CIE S 017:2020 and NIST photometry/radiometry references.

- [ ] **Step 2: Write `spectral-power-distribution.md`**

Define spectral distributions, absolute versus relative SPDs, integration/discretization, spectral descriptors, additive mixing, metamerism, and measurement/representation caveats. Cite CIE S 017:2020, CIE 015:2018, and NIST material.

- [ ] **Step 3: Write `color-science.md`**

Explain the SPD-to-XYZ path, standard observers, chromaticity, CCT/Duv, metamerism, and color rendition without duplicating project policy. Cite CIE 015:2018, CIE S 017:2020, NIST Ohno material, ANSI/IES TM-30-24, and ANSI/IES TM-40-24.

- [ ] **Step 4: Verify scientific and documentation consistency**

Check that equations use consistent symbols/units, each source URL resolves to an authoritative publisher, no chapter claims that chromaticity uniquely determines a spectrum, and no hardware part is selected by inference.

### Task 2: Integrate the theory section into repository navigation

**Files:**
- Modify: `docs/README.md`
- Modify: `docs/02-repository-organization.md`
- Modify: `docs/agent-handoff.md`

**Interfaces:**
- Consumes: the three theory chapters from Task 1.
- Produces: discoverable documentation and an accurate current-state handoff.

- [ ] **Step 1: Update documentation navigation**

Keep the numbered core documents as the architecture/policy reading path, then add a separate `Theory foundations` section linking the three chapters in their recommended learning order.

- [ ] **Step 2: Update repository organization**

State that `docs/theory/` owns source-backed scientific background while root numbered docs retain cross-system architecture, standards, and project policy.

- [ ] **Step 3: Update handoff**

Record that the first theory foundation exists, while preserving the current active goal and open hardware decisions.

- [ ] **Step 4: Verify final diff**

Confirm that the branch changes are documentation-only, internal links resolve, no unrelated file changed, and the next exact hardware-selection step remains unchanged.

- [ ] **Step 5: Commit**

Commit the completed slice as `docs: add sourced theory foundations`.
