# Project Phase Roadmap — FeTVControl

**Document:** PLAN-001  
**Revision:** A  
**Status:** Active

---

## Overview

This document defines the phased development plan for the FeTVControl project. Each phase has defined entry criteria, deliverables, exit criteria, and primary tools. The phases are designed to mirror the controls development lifecycle an autonomy engineer would execute on a real subsystem program.

```
Phase 1          Phase 2          Phase 3          Phase 4
System Study ──► Low-Fi Model ──► Validation  ──► Med-Fi Model
                                                        │
                                                        ▼
                                    Phase 7    Phase 5          Phase 6
                                    Ops Mgr ◄── Control Law ──► FDIR
```

---

## Phase 1 — System Study & Requirements

**Goal:** Understand the physical system deeply enough to write requirements and define the model architecture before writing a line of code.

**Entry Criteria:** Project decision made (this document exists).

**Deliverables:**
- `docs/requirements.md` — System and subsystem requirements (SYS-REQ-001) ✅
- `docs/interface_definition.md` — Signal interface definitions (ICD-001) ✅
- `docs/literature_review.md` — Annotated bibliography with Phase 1 action items ✅
- `phase1_system_study/parameter_estimates.md` — Initial plant parameter estimates from literature
- `phase1_system_study/system_sketch.md` — Free-body diagrams, equations of motion derivation (written out, not coded)

**Exit Criteria:**
- [ ] All TBDs in requirements.md resolved or have a clear path to resolution
- [ ] Motor, gearbox, nozzle parameter estimates populated with sources
- [ ] Equations of motion written out for Phase 2 implementation
- [ ] Phase 2 model architecture decided (state vector, integration method)

**Primary Tools:** Markdown, pencil/paper, Python for unit-check calculations

---

## Phase 2 — Low-Fidelity Plant Model

**Goal:** Implement the core physics of the EMA/nozzle system in Python. Fast, scrappy, easy to iterate. Get a closed-loop response and verify it looks physically reasonable.

**Entry Criteria:** Phase 1 exit criteria met.

**Deliverables:**
- `phase2_lowfi_model/python/motor_model.py` — BLDC motor electrical + torque model
- `phase2_lowfi_model/python/gearbox_model.py` — Rigid gearbox (fixed ratio, no compliance)
- `phase2_lowfi_model/python/nozzle_model.py` — Rigid-body nozzle dynamics (2-DOF)
- `phase2_lowfi_model/python/ema_plant.py` — Integrated plant model
- `phase2_lowfi_model/python/simple_pid.py` — Baseline PID controller for sanity check
- `phase2_lowfi_model/python/simulate.py` — Simulation runner, step/sweep inputs
- `phase2_lowfi_model/notebooks/01_step_response.ipynb` — Step response plots, basic performance check
- `phase2_lowfi_model/notebooks/02_frequency_response.ipynb` — Open/closed loop Bode plots
- `results/phase2/` — Saved plots

**Exit Criteria:**
- [ ] Step response meets PRF-005 (≤150ms settling)
- [ ] Closed-loop bandwidth ≥10 Hz (PRF-002)
- [ ] Phase and gain margins meet PRF-006/007
- [ ] Disturbance rejection qualitatively reasonable under sinusoidal side-load

**Primary Tools:** Python, `scipy`, `numpy`, `matplotlib`, `python-control`

---

## Phase 3 — Model Validation

**Goal:** Validate the low-fidelity model against published data. Quantify model error. This is what separates real engineering from simulation hobbyism.

**Entry Criteria:** Phase 2 exit criteria met. Validation dataset identified in Phase 1.

**Deliverables:**
- `phase3_validation/data/` — Reference datasets (publicly available EMA characterization data)
- `phase3_validation/data/README.md` — Data provenance, source citations, format description
- `phase3_validation/python/data_loader.py` — Data ingestion and preprocessing
- `phase3_validation/python/model_fit.py` — Parameter identification / fitting routines
- `phase3_validation/python/validation_metrics.py` — RMS error, frequency response comparison
- `phase3_validation/notebooks/01_parameter_identification.ipynb`
- `phase3_validation/notebooks/02_step_response_validation.ipynb`
- `phase3_validation/notebooks/03_frequency_response_validation.ipynb`
- `results/phase3/` — Validation plots with error metrics annotated

**Exit Criteria:**
- [ ] Model reproduces published step response within 15% RMS error (MOD-014)
- [ ] Frequency response within 3 dB of reference across bandwidth
- [ ] Identified parameter set documented with confidence bounds
- [ ] Residual error characterized and root-caused (modeling gaps documented)

**Primary Tools:** Python, `scipy.optimize`, `pandas`, `matplotlib`

---

## Phase 4 — Medium-Fidelity Plant Model

**Goal:** Add the nonlinearities that matter. Port the validated physics core to Rust. This is where the Python/Rust architecture boundary is established.

**Entry Criteria:** Phase 3 exit criteria met. Rust toolchain set up.

**Deliverables:**
- `phase4_medfi_model/python/` — Extended Python model (backlash, flex joint, thermal, broadband disturbance)
- `phase4_medfi_model/rust/` — Rust crate: `ema_plant`
  - `src/motor.rs` — BLDC motor model
  - `src/gearbox.rs` — Gearbox with backlash and compliance
  - `src/nozzle.rs` — Nozzle + flex joint dynamics
  - `src/thermal.rs` — Winding temperature model
  - `src/disturbance.rs` — Side-load disturbance generator
  - `src/plant.rs` — Integrated plant (top-level step function)
  - `src/lib.rs` — PyO3 bindings
- `phase4_medfi_model/notebooks/01_nonlinearity_study.ipynb` — Effect of backlash, flex joint on step response
- `phase4_medfi_model/notebooks/02_rust_vs_python_comparison.ipynb` — Verify Rust and Python implementations agree

**Exit Criteria:**
- [ ] Rust plant model output matches Python model within floating-point tolerance
- [ ] Backlash nonlinearity visibly affects step response (limit cycling in underdamped case)
- [ ] Thermal model shows correct winding temperature rise under sustained current
- [ ] PyO3 bindings working — Python harness can call Rust plant step function

**Primary Tools:** Rust (`nalgebra`, `serde`, `PyO3`), Python

---

## Phase 5 — Control Law Design & Implementation

**Goal:** Design a control law against the medium-fidelity plant. Implement it in Rust. Compare classical vs. modern approaches.

**Entry Criteria:** Phase 4 exit criteria met.

**Deliverables:**
- `phase5_control/simulink/` — Simulink or OpenModelica model for control design and frequency-domain analysis
- `phase5_control/rust/` — Rust crate: `ema_controller`
  - `src/pid.rs` — Cascaded PID (outer position, inner current)
  - `src/lqr.rs` — LQR state feedback (gain matrix pre-computed from Simulink/Python)
  - `src/feedforward.rs` — Reference model feedforward
  - `src/controller.rs` — Controller trait and dispatcher
  - `src/lib.rs` — PyO3 bindings
- Gain tables per operational phase (JSON, loaded at runtime by phase_id)
- `phase5_control/notebooks/01_gain_design.ipynb` — LQR/PID design, gain selection rationale
- `phase5_control/notebooks/02_closed_loop_performance.ipynb` — Full closed-loop sim with Rust plant + Rust controller
- `phase5_control/notebooks/03_robustness_analysis.ipynb` — Gain/phase margin vs. plant uncertainty

**Exit Criteria:**
- [ ] All Phase 2 performance requirements met with medium-fidelity plant (PRF-001 through PRF-007)
- [ ] LQR outperforms baseline PID on disturbance rejection metric (quantified)
- [ ] Gain tables defined for all 5 operational phases
- [ ] Controller runs in Rust, driven from Python sim harness via PyO3

**Primary Tools:** Simulink / OpenModelica, Rust, Python `control` library

---

## Phase 6 — FDIR Design & Implementation

**Goal:** Enumerate fault modes, design detection and isolation logic, implement in Rust, verify with fault injection testing.

**Entry Criteria:** Phase 5 exit criteria met.

**Deliverables:**
- `phase6_fdir/rust/` — Rust crate: `ema_fdir`
  - `src/faults.rs` — Fault enum definitions with metadata
  - `src/detectors/` — One module per fault type
    - `phase_loss.rs`
    - `jam.rs`
    - `encoder_dropout.rs`
    - `thermal_overtemp.rs`
    - `runaway.rs`
    - `backlash_degradation.rs`
  - `src/isolator.rs` — Isolation logic (fault disambiguation)
  - `src/responder.rs` — Response action dispatch by fault + phase
  - `src/state_machine.rs` — Top-level FDIR state machine
  - `src/lib.rs`
- `phase6_fdir/fault_injection/` — Python scripts to inject each fault type into sim
- `phase6_fdir/notebooks/01_fault_injection_results.ipynb` — Detection latency, false positive rate per fault
- `results/phase6/` — FDIR test matrix results

**Fault Coverage Matrix:**

| Fault | Detector | Isolation | Response | Phase-Aware |
|-------|----------|-----------|----------|-------------|
| Phase Loss | Current signature asymmetry | Motor drive monitor | Safe mode | Yes |
| Mechanical Jam | Pos error + zero velocity | Force/current crosscheck | Force hold → abort | Yes |
| Encoder Dropout | Signal validity + rate limit | Primary vs. redundant compare | Switch encoder | No |
| Thermal Overtemp | Thermal model threshold | Temperature trend | Current derate | No |
| Runaway | Hardstop exceedance | Position rate | Hard inhibit | No |
| Backlash Growth | Freq response deadband shift | Trend monitor | Advisory flag | No |

**Exit Criteria:**
- [ ] All RLB requirements met (RLB-001 through RLB-007)
- [ ] Each fault detected within required latency in injection test
- [ ] False positive rate < 1 per 1000 sim runs under nominal conditions
- [ ] FDIR state machine exhaustively handles all fault/phase combinations

**Primary Tools:** Rust (`thiserror`, `log`), Python (fault injection harness)

---

## Phase 7 — Operational Phase Manager & Integration

**Goal:** Tie everything together. Implement the operational state machine that transitions through OP-1 to OP-5, switching gain tables and FDIR thresholds. Run a full end-to-end simulation of a representative mission segment.

**Entry Criteria:** Phases 5 and 6 exit criteria met.

**Deliverables:**
- `phase7_ops/rust/` — Rust crate: `ema_ops`
  - `src/phase_manager.rs` — Operational phase state machine and transition logic
  - `src/checkout.rs` — OP-1 pre-ignition checkout sequence
  - `src/integration.rs` — Top-level integration of plant, controller, FDIR, phase manager
- `sim_harness/python/` — Final simulation harness
  - `run_mission.py` — End-to-end mission simulation script
  - `plotter.py` — Comprehensive results plotter
  - `report_generator.py` — Auto-generate a summary PDF/HTML from results
- `phase7_ops/notebooks/01_full_mission_simulation.ipynb`
- `results/phase7/` — Final simulation results, performance summary

**Exit Criteria:**
- [ ] Full simulated mission (OP-1 through OP-5) runs without assertion failures
- [ ] Controller maintains performance requirements through all phases
- [ ] FDIR correctly activates and responds to injected faults in each phase
- [ ] Summary report generated with key performance metrics

**Primary Tools:** Rust, Python

---

## Dependency Graph

```
Phase 1 (Docs & Study)
    └── Phase 2 (Low-Fi Python Model)
            └── Phase 3 (Validation)
                    └── Phase 4 (Med-Fi + Rust Port)
                            ├── Phase 5 (Control Law)
                            │       └── Phase 7 (Ops Integration)
                            └── Phase 6 (FDIR)
                                    └── Phase 7 (Ops Integration)
```

---

## Estimated Effort (Personal Project Pace)

| Phase | Estimated Hours |
|-------|----------------|
| 1 | 8–12 hrs |
| 2 | 15–20 hrs |
| 3 | 10–15 hrs |
| 4 | 20–30 hrs |
| 5 | 25–35 hrs |
| 6 | 20–25 hrs |
| 7 | 15–20 hrs |
| **Total** | **~115–160 hrs** |

At 5–8 hours/week this is a 4–8 month project, which is realistic for a personal portfolio project at this depth.
