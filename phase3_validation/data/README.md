# Validation Data — Provenance & Format

## Purpose
This directory holds publicly available EMA characterization data used for Phase 3 model validation. All data must be from public sources (NASA NTRS, published papers, open university repositories).

## Data Sources

| File | Source | Description | DOI / URL |
|------|--------|-------------|-----------|
| TBD  | TBD    | TBD         | TBD       |

## Phase 1 Action
Search NASA NTRS (https://ntrs.nasa.gov) for:
- "electromechanical actuator characterization"
- "EMA TVC step response"
- "brushless DC motor aerospace characterization"

Download any freely available technical reports with step response or frequency response data for a flight-class BLDC motor or EMA system. Document the source in this table before using the data.

## Format Convention
All data should be saved as CSV with a header row:
```
time_s, position_deg, current_a, voltage_v, [other columns as available]
```
Include a companion `<filename>_meta.json` with source, units, sample rate, and any relevant test conditions.
