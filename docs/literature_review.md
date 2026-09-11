# Literature Review — EMA/TVC Subsystem

**Document:** LIT-001  
**Revision:** A  
**Status:** Draft — to be expanded during Phase 1

---

## 1. Purpose

This document catalogs the key references used to develop system requirements, plant model parameters, and control design choices for the FeTVControl project. Entries are annotated with their specific relevance to each project phase.

---

## 2. TVC System Architecture & EMA Design

### 2.1 General TVC References

| Ref ID | Source | Title | Relevance |
|--------|--------|-------|-----------|
| REF-001 | NASA TM-2010-216908 | *Thrust Vector Control for Nuclear Thermal Rockets* | System-level TVC architecture, actuator sizing methodology |
| REF-002 | NASA CR-195477 | *Electromechanical Actuation for Thrust Vector Control Applications* | Primary EMA architecture reference; motor sizing, gearbox ratios, load cases |
| REF-003 | AIAA 2004-4326 | *Electromechanical Actuators for TVC on Expendable Launch Vehicles* | EMA vs EHA tradeoff, bandwidth requirements, step response data |
| REF-004 | ESA ECSS-E-ST-33-11C | *Space Engineering: Electrical and Electronic* | Fault detection requirements, reliability standards |

### 2.2 EMA Characterization Data

| Ref ID | Source | Notes |
|--------|--------|-------|
| REF-005 | NASA STI | Several BLDC motor characterization reports available via NTRS — search "electromechanical actuator characterization" |
| REF-006 | University theses (MIT, UT Austin) | Several MS theses on EMA modeling for aerospace available open access — good for parameter identification |

---

## 3. Motor & Drive Modeling

| Ref ID | Source | Title | Relevance |
|--------|--------|-------|-----------|
| REF-010 | Krause et al. | *Analysis of Electric Machinery and Drive Systems* | BLDC motor d-q model, electrical time constants, torque equations |
| REF-011 | Mohan et al. | *Power Electronics* | Inverter modeling, PWM effects on current ripple |
| REF-012 | NASA TP-2018-219857 | *Motor Drive Thermal Modeling* | Winding temperature models for flight motors |

**Key Parameters to Extract (Phase 2 targets):**
- Motor torque constant K_t (Nm/A)
- Motor back-EMF constant K_e (V·s/rad)
- Winding resistance R (Ω)
- Winding inductance L (H)
- Rotor inertia J_m (kg·m²)
- Viscous friction coefficient B (Nm·s/rad)

---

## 4. Gearbox & Mechanical Transmission

| Ref ID | Source | Notes |
|--------|--------|-------|
| REF-020 | Merritt, H.E. | *Hydraulic Control Systems* (Ch. applicable to mechanical analogies) | Backlash and compliance modeling — directly applicable to gearbox |
| REF-021 | Nordin et al. | *Nonlinear Backlash Compensation* | Backlash identification and compensation algorithms |
| REF-022 | Literature TBD | Planetary gearbox efficiency data for aerospace actuators | Phase 4 parameter — search NASA NTRS |

**Key Parameters to Extract:**
- Gear ratio N
- Gearbox torsional stiffness K_g (Nm/rad)
- Backlash deadband δ (rad)
- Gearbox efficiency η

---

## 5. Flex Joint / Gimbal Bearing

| Ref ID | Source | Notes |
|--------|--------|-------|
| REF-030 | NASA SP-8120 | *Liquid Rocket Engine Nozzles* | Nozzle mass properties, gimbal geometry |
| REF-031 | Various | Flex joint stiffness typically ranges 500–5000 Nm/rad — derive from engine thrust class |

**Key Parameters to Extract:**
- Flex joint rotational stiffness K_fj (Nm/rad)
- Flex joint damping C_fj (Nm·s/rad)
- Nozzle mass m_n (kg)
- Nozzle moment of inertia I_n (kg·m²)

---

## 6. Side-Load Environment

| Ref ID | Source | Notes |
|--------|--------|-------|
| REF-040 | NASA SP-8075 | *Solid Rocket Motor Nozzles* | Side-load characterization — applicable by analogy to liquid engines |
| REF-041 | Dumnov (1996) | *Unsteady Side Loads Acting on the Nozzle with Developed Separation Zone* | Classic liquid rocket side-load paper |
| REF-042 | Nave & Coffey (1973) | *Sea Level Side Loads in High-Area-Ratio Rocket Engines* | Foundational side-load data |

**Modeling approach:** Use a Dryden wind turbulence model (well-established, implemented in scipy) as a proxy for broadband combustion side-loads with appropriate spectral shaping in Phase 4.

---

## 7. Control Design References

| Ref ID | Source | Notes |
|--------|--------|-------|
| REF-050 | Franklin, Powell, Emami-Naeini | *Feedback Control of Dynamic Systems* | Core control textbook — PID design, root locus, frequency response |
| REF-051 | Skogestad & Postlethwaite | *Multivariable Feedback Design* | H-infinity design reference for Phase 5 extension |
| REF-052 | Wie, B. | *Space Vehicle Dynamics and Control* | Attitude control context for TVC, coupling to vehicle model |

---

## 8. FDIR References

| Ref ID | Source | Notes |
|--------|--------|-------|
| REF-060 | Isermann, R. | *Fault Diagnosis Systems* | Model-based FDIR methodology, observer-based detection |
| REF-061 | Patton, Frank, Clark | *Issues of Fault Diagnosis for Dynamic Systems* | Residual generation, isolation logic design |
| REF-062 | NASA-HDBK-1002 | *Fault Management Handbook* | NASA fault management philosophy, FDIR tiering |

---

## 9. Rust / Software References

| Ref ID | Source | Notes |
|--------|--------|-------|
| REF-070 | Ferrous Systems | *The Embedded Rust Book* | Rust for embedded/real-time systems patterns |
| REF-071 | PyO3 Documentation | https://pyo3.rs | Python/Rust FFI bridge |
| REF-072 | nalgebra crate | https://nalgebra.org | Linear algebra in Rust |

---

## 10. Phase 1 Actions

- [ ] Download and read REF-001, REF-002, REF-003 — extract actuator sizing data and step response benchmark
- [ ] Search NASA NTRS for EMA characterization test reports — extract motor parameter ranges
- [ ] Extract nozzle mass properties from REF-030 for a representative engine class (e.g., ~100 kN thrust)
- [ ] Identify at least one published frequency response dataset for an EMA to use as Phase 3 validation target
- [ ] Read REF-060 and REF-062 to inform FDIR architecture decisions before Phase 6
