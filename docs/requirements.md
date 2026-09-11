# System Requirements — FeTVControl EMA/TVC Subsystem

**Document:** SYS-REQ-001  
**Revision:** A  
**Status:** Draft

---

## 1. Purpose

This document defines the system-level and subsystem-level requirements for the Electromechanical Actuator (EMA) used to drive nozzle gimbal deflection in a Thrust Vector Control (TVC) system for a liquid rocket engine. These requirements govern the plant model, control law, and FDIR development carried out across all project phases.

Requirements are derived from publicly available literature on TVC systems, EMA characterization studies, and general aerospace control system standards (MIL-STD-1797, ECSS-E-ST-60C).

---

## 2. System Context

The EMA subsystem sits at the boundary between the Flight Control System (FCS) and the propulsion system. It receives digital position commands from the flight computer and produces mechanical nozzle deflection, which generates the control moment used to steer the vehicle.

```
Flight Computer ──[position cmd]──► EMA ──[nozzle angle]──► Vehicle Dynamics
                ◄─[position fdbk]──     ◄─[side loads]────
                ◄─[fault flags]───
```

The EMA consists of:
- Power Electronics / Motor Drive
- Brushless DC Motor (BLDC)
- Gearbox (fixed ratio)
- Linear Actuator / Push Rod
- Nozzle Flex Joint / Gimbal Bearing
- Position Encoder (primary + redundant)
- Thermal Monitoring

---

## 3. Performance Requirements

### 3.1 Position Control

| ID | Requirement | Value | Rationale |
|----|-------------|-------|-----------|
| PRF-001 | Maximum nozzle deflection | ±6° | Typical TVC authority for liquid engines |
| PRF-002 | Closed-loop bandwidth (-3dB) | ≥ 10 Hz | Vehicle attitude control loop requires fast actuation |
| PRF-003 | Maximum slew rate | ≥ 20°/s | Worst-case attitude maneuver demand |
| PRF-004 | Steady-state position error | ≤ 0.05° | Pointing accuracy requirement |
| PRF-005 | Step response settling time (±2%) | ≤ 150 ms | For a 1° step command |
| PRF-006 | Closed-loop phase margin | ≥ 45° | Classical stability margin |
| PRF-007 | Closed-loop gain margin | ≥ 6 dB | Classical stability margin |

### 3.2 Load Handling

| ID | Requirement | Value | Rationale |
|----|-------------|-------|-----------|
| PRF-010 | Peak side-load rejection | ≤ 0.1° position error at rated side load | Combustion-induced disturbance rejection |
| PRF-011 | Rated side-load force | TBD (from propulsion team) | Placeholder — derive from engine thrust level |
| PRF-012 | Structural stiffness (flex joint) | TBD | Model parameter to be identified from literature |

### 3.3 Thermal

| ID | Requirement | Value | Rationale |
|----|-------------|-------|-----------|
| PRF-020 | Maximum motor winding temperature | ≤ 155°C (Class F insulation) | Motor derating threshold |
| PRF-021 | Thermal derating activation | ≤ 140°C | 15°C margin before hard limit |

---

## 4. Interface Requirements

| ID | Requirement |
|----|-------------|
| ICD-001 | EMA shall accept position commands as a floating-point value in degrees at a minimum rate of 100 Hz |
| ICD-002 | EMA shall output measured nozzle position in degrees at the same rate as command input |
| ICD-003 | EMA shall output a fault status word containing active fault flags, fault severity, and last-known-good position |
| ICD-004 | EMA shall output motor winding temperature (modeled or measured) |
| ICD-005 | EMA shall output motor phase currents (all three phases) |
| ICD-006 | All outputs shall have defined valid ranges; values outside range shall set a validity flag |

---

## 5. Reliability & FDIR Requirements

| ID | Requirement |
|----|-------------|
| RLB-001 | EMA shall detect a mechanical jam within 50 ms of fault onset |
| RLB-002 | EMA shall detect motor phase loss within 20 ms of fault onset |
| RLB-003 | EMA shall detect encoder dropout within one command cycle (≤ 10 ms) |
| RLB-004 | Upon detection of a non-recoverable fault, EMA shall command safe-mode position (neutral, 0°) within 100 ms |
| RLB-005 | EMA shall support a limp-home mode for degraded-but-operable fault states |
| RLB-006 | FDIR logic shall be phase-aware — detection thresholds and responses shall differ by operational phase |
| RLB-007 | FDIR shall produce an isolation verdict (which fault) in addition to a detection flag |

---

## 6. Model Fidelity Requirements

### 6.1 Low-Fidelity Model (Phase 2)
| ID | Requirement |
|----|-------------|
| MOD-001 | Shall capture rigid-body nozzle dynamics (2-DOF) |
| MOD-002 | Shall include DC motor electrical model (back-EMF, electrical time constant) |
| MOD-003 | Shall include fixed-ratio gearbox with no compliance |
| MOD-004 | Shall support sinusoidal side-load disturbance injection |

### 6.2 Medium-Fidelity Model (Phase 4)
| ID | Requirement |
|----|-------------|
| MOD-010 | Shall add gearbox backlash and torsional compliance |
| MOD-011 | Shall add flex joint structural stiffness and damping |
| MOD-012 | Shall add motor winding thermal model |
| MOD-013 | Shall support broadband side-load disturbance (Dryden turbulence model or published combustion PSD) |
| MOD-014 | Model shall reproduce published step response data within 15% RMS error |

---

## 7. Operational Phases

| Phase ID | Name | Description |
|----------|------|-------------|
| OP-1 | Pre-Ignition Checkout | Power-on self-test, actuator sweep verification, encoder health check |
| OP-2 | Ignition / Startup Transient | Engine startup, first fire, increased side-load environment |
| OP-3 | Mainstage | Nominal full-thrust operation, nominal control authority |
| OP-4 | Throttle Transient | Thrust change command, dynamic load variation |
| OP-5 | Shutdown | Engine cutoff, reduced loads, return to neutral |

FDIR thresholds and control gains shall be defined per operational phase.

---

## 8. Open Items / TBDs

| Item | Owner | Target Phase |
|------|-------|-------------|
| Rated side-load force (PRF-011) | Derive from engine thrust assumptions | Phase 2 |
| Flex joint stiffness (PRF-012) | Literature identification | Phase 3 |
| Gearbox ratio | Literature identification | Phase 2 |
| Motor rated torque and speed | Literature identification | Phase 2 |
