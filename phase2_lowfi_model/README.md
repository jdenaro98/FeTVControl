# Phase 2 — Low-Fidelity Plant Model

## What this does
Python implementation of the EMA/TVC plant model. Rigid motor, rigid gearbox, rigid-body nozzle. Sinusoidal side-load disturbance. Baseline PID controller for sanity check.

## How to run
```bash
cd phase2_lowfi_model/python
pip install numpy scipy matplotlib python-control
python simulate.py
```

## What to expect
- Step response plot showing ~100–150ms settling time
- Bode plot showing ≥10 Hz closed-loop bandwidth
- Side-load disturbance rejection plot

## Known limitations
- No gearbox backlash or compliance (added in Phase 4)
- No thermal model
- Motor model simplified to single equivalent circuit (not d-q)
- Side-load is purely sinusoidal (broadband added in Phase 4)

## Entry criteria met
See `docs/phase_roadmap.md` Phase 1 exit criteria.

## Exit criteria
See `docs/phase_roadmap.md` Phase 2 exit criteria.
