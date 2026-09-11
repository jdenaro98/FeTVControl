# Phase 3 — Model Validation

## What this does
Validates the Phase 2 low-fidelity plant model against publicly available EMA characterization data. Performs parameter identification and quantifies model error.

## Data
See `data/README.md` for dataset descriptions and provenance.

## How to run
```bash
cd phase3_validation/python
pip install numpy scipy matplotlib pandas python-control
python data_loader.py        # verify data loaded correctly
python model_fit.py          # run parameter identification
python validation_metrics.py # compute error metrics
```

## What to expect
- Overlaid step response: model vs. reference data
- Frequency response comparison (Bode magnitude and phase)
- RMS error metric — target ≤15% (MOD-014)

## Exit criteria
See `docs/phase_roadmap.md` Phase 3 exit criteria.
