# Phase 4 — Medium-Fidelity Plant Model

## What this does
Extends the Phase 2 Python model with nonlinearities (gearbox backlash, flex joint compliance, thermal model, broadband side-load), then ports the validated physics core to Rust. Establishes the Python/Rust architecture boundary.

## Structure
- `python/` — Extended Python model (reference implementation)
- `rust/` — Rust crate `ema_plant` with PyO3 bindings

## How to run
```bash
# Python
cd phase4_medfi_model/python
python simulate_medfi.py

# Rust (build + test)
cd phase4_medfi_model/rust
cargo build
cargo test

# Verify Rust matches Python
cd phase4_medfi_model
python notebooks/02_rust_vs_python_comparison.py
```

## Exit criteria
See `docs/phase_roadmap.md` Phase 4 exit criteria.
