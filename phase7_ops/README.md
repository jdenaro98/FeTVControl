# Phase 7 — Operational Phase Manager & Integration

## What this does
Implements the operational phase state machine (OP-1 through OP-5) and integrates the plant model, controller, and FDIR into a full end-to-end simulation. Runs a complete representative mission segment.

## Structure
- `rust/` — Rust crate `ema_ops` (phase manager + integration layer)

## How to run
```bash
# Full mission simulation
cd sim_harness/python
python run_mission.py

# View results
python plotter.py results/phase7/mission_run_<timestamp>/
```

## Exit criteria
See `docs/phase_roadmap.md` Phase 7 exit criteria.
