# Contributing

OpenSpectralLight favors small, reviewable vertical slices over broad speculative
framework work.

Before changing firmware, read `docs/03-coding-standards.md`. Before changing
module boundaries or a hardware interface, read the architecture documents and
record a durable architectural decision in `docs/adr/` when appropriate.

## Development rules

1. Keep one goal per branch/PR.
2. Write deterministic host tests first for portable behavior.
3. Do not claim host tests prove electrical, optical, thermal, RF, or timing behavior.
4. Keep MCU/vendor details out of portable application logic.
5. Do not add cloud, Internet dependencies, OTA/FOTA, or Wi-Fi as incidental features.
6. Do not create shared/common abstractions until at least two real consumers need them.
7. Update documentation when a public interface, hardware boundary, or measured capability changes.

## Baseline verification

```sh
cmake --preset host-debug
cmake --build --preset host-debug
ctest --preset host-debug --output-on-failure
```
