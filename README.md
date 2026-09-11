# FeTVControl

> A model-based controls development project for an Electromechanical Actuator (EMA) driving a gimbaled rocket engine nozzle for Thrust Vector Control (TVC).

---

## Project Overview

This project follows the full controls development lifecycle that an autonomy or GNC engineer embedded with a subsystem team would execute — from learning the system and building low-fidelity plant models, through model validation against real data, control law implementation, gain tuning, FDIR design, and phase-aware operational logic.

The system under study is the **EMA that drives nozzle gimbal deflection** on a liquid rocket engine. The EMA receives position commands from the flight computer, drives a brushless DC motor through a gearbox, and deflects the nozzle against inertial, aerodynamic, and structural loads.

The project is implemented across **Python** (simulation harness, validation, data processing), **Rust** (plant model physics engine, control law execution, FDIR state machine), and **Simulink/OpenModelica** (control design and frequency-domain analysis).

---

## Repository Structure

```
FeTVControl/
├── docs/                         # Requirements, interface definitions, design docs
│   ├── requirements.md           # System and subsystem requirements
│   ├── interface_definition.md   # Subsystem boundary and signal definitions
│   ├── literature_review.md      # Key references and findings
│   └── phase_roadmap.md          # Full phased project plan
│
├── phase1_system_study/          # Literature review notes, requirements development
│
├── phase2_lowfi_model/           # Low-fidelity Python plant model
│   ├── python/
│   └── notebooks/
│
├── phase3_validation/            # Model validation against public data
│   ├── data/                     # Public reference datasets
│   ├── python/
│   └── notebooks/
│
├── phase4_medfi_model/           # Medium-fidelity model with nonlinearities
│   ├── python/                   # Python reference implementation
│   └── rust/                     # Rust physics engine core
│
├── phase5_control/               # Control law design and implementation
│   ├── simulink/                 # Simulink/OpenModelica models
│   └── rust/                     # Rust control law implementation
│
├── phase6_fdir/                  # Fault Detection, Isolation and Response
│   └── rust/                     # Rust FDIR state machine
│
├── phase7_ops/                   # Operational phase manager and integration
│   └── rust/
│
├── sim_harness/                  # Python simulation driver, plotting, test orchestration
│   └── python/
│
└── results/                      # Plots, validation reports, analysis outputs
```

---

## Phased Approach

| Phase | Title | Primary Tools | Status |
|-------|-------|--------------|--------|
| 1 | System Study & Requirements | Markdown, Python (quick math) | 🟡 In Progress |
| 2 | Low-Fidelity Plant Model | Python, scipy, numpy | ⬜ Not Started |
| 3 | Model Validation | Python, matplotlib | ⬜ Not Started |
| 4 | Medium-Fidelity Plant Model | Python → Rust port | ⬜ Not Started |
| 5 | Control Law Design & Implementation | Simulink / OpenModelica, Rust | ⬜ Not Started |
| 6 | FDIR Design & Implementation | Rust | ⬜ Not Started |
| 7 | Operational Phase Manager | Rust | ⬜ Not Started |

---

## Tech Stack

### Python
- `scipy` / `numpy` — dynamics simulation, signal processing
- `matplotlib` / `plotly` — visualization
- `PyO3` — Python/Rust FFI bridge
- `pandas` — data handling for validation

### Rust
- `nalgebra` — linear algebra, state-space math
- `ndarray` — N-dimensional arrays
- `PyO3` — expose Rust core to Python harness
- `serde` / `serde_json` — telemetry and config serialization
- `thiserror` / `anyhow` — idiomatic error handling
- `log` / `env_logger` — structured logging

### Control Design
- MATLAB/Simulink (if licensed) or OpenModelica (open source)
- Python `control` library for frequency response analysis

---

## Background & Motivation

This project demonstrates the full model-based controls development lifecycle as practiced by GNC and autonomy engineers in the aerospace industry. The EMA/TVC subsystem was chosen because:

- It is directly relevant to liquid rocket engine systems
- It has rich nonlinear dynamics (backlash, flex joint compliance, thermal effects)
- It has credible and interesting fault modes for FDIR development
- Sufficient public literature exists for model validation without proprietary data
- It maps cleanly to the subsystem boundary that GNC/autonomy engineers interface with

---

## References

See `docs/literature_review.md` for annotated references.

---

## License

MIT