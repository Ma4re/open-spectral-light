# Hardware

Electronics are organized by physical responsibility, not by component part
number. Each real PCB gains an explicit revision directory only when a reviewed
design exists.

Current module boundaries:

- `controller/` — low-voltage control electronics and programming/debug boundary.
- `power/` — LED current regulation and high-power electrical protection.
- `light-engine/` — emitters, strings, MCPCB, and thermal/optical source geometry.
- `sensor/` — optional physical measurement daughterboard(s).

Electrical interfaces between these modules are architecture contracts once
frozen and must be documented alongside the affected hardware revisions.
