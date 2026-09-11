# Phase 5 — Control Law Design & Implementation

## What this does
Designs the control law using frequency-domain methods (Simulink or OpenModelica + Python control library), then implements the controller in Rust. Compares cascaded PID vs. LQR. Defines phase-specific gain tables.

## Structure
- `simulink/` — Simulink (.slx) or OpenModelica (.mo) control design models
- `rust/` — Rust crate `ema_controller` with PyO3 bindings

## Exit criteria
See `docs/phase_roadmap.md` Phase 5 exit criteria.
