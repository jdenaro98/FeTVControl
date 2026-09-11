# Phase 6 — FDIR Design & Implementation

## What this does
Implements the Fault Detection, Isolation, and Response system as a Rust state machine. Covers all fault modes defined in `docs/requirements.md`. Includes fault injection testing via Python harness.

## Structure
- `rust/` — Rust crate `ema_fdir`
- `fault_injection/` — Python scripts to inject faults into the simulation
- `notebooks/` — FDIR test results and analysis

## Fault Coverage
See FDIR fault coverage matrix in `docs/phase_roadmap.md` Phase 6 section.

## Exit criteria
See `docs/phase_roadmap.md` Phase 6 exit criteria.
