# Plant Parameter Estimates — Phase 1

**Document:** PARAM-001  
**Revision:** A  
**Status:** Draft — populate from literature during Phase 1

---

## Purpose

This document collects initial parameter estimates for the EMA plant model, sourced from public literature. All values here are starting points for Phase 2 implementation. Parameters marked TBD require literature research to fill in. Parameters marked ESTIMATE are engineering judgments pending validation in Phase 3.

---

## Motor Parameters (BLDC)

Representative values for a flight-class BLDC motor in the 1–5 kW range, suitable for a ~100 kN thrust class engine TVC actuator.

| Parameter | Symbol | Value | Unit | Source | Confidence |
|-----------|--------|-------|------|--------|------------|
| Torque constant | K_t | TBD | Nm/A | REF-002/005 | — |
| Back-EMF constant | K_e | TBD | V·s/rad | REF-002/005 | — |
| Winding resistance (per phase) | R | TBD | Ω | REF-002/005 | — |
| Winding inductance (per phase) | L | TBD | H | REF-002/005 | — |
| Rotor inertia | J_m | TBD | kg·m² | REF-002/005 | — |
| Viscous friction | B | TBD | Nm·s/rad | ESTIMATE | Low |
| Rated speed | ω_rated | TBD | RPM | REF-002/005 | — |
| Rated torque | τ_rated | TBD | Nm | REF-002/005 | — |
| Number of poles | p | TBD | — | REF-002/005 | — |
| Thermal resistance (winding-to-case) | R_th | TBD | °C/W | REF-012 | — |
| Thermal capacitance (winding) | C_th | TBD | J/°C | REF-012 | — |

**Phase 1 Action:** Pull from NASA CR-195477 (REF-002) and any available motor characterization data (REF-005). Cross-check K_t and K_e using K_t = K_e (SI units).

---

## Gearbox Parameters

| Parameter | Symbol | Value | Unit | Source | Confidence |
|-----------|--------|-------|------|--------|------------|
| Gear ratio | N | TBD | — | REF-002/003 | — |
| Torsional stiffness | K_g | TBD | Nm/rad | REF-020/021 | — |
| Backlash deadband | δ | TBD | rad | REF-021 | — |
| Efficiency | η | ~0.90 | — | ESTIMATE | Medium |
| Output shaft inertia | J_g | TBD | kg·m² | REF-002 | — |

**Notes:** Typical gear ratios for EMA/TVC are in the 20:1–100:1 range. Backlash in flight-class planetary gearboxes is typically <0.1° on the output shaft.

---

## Nozzle / Gimbal Parameters

Representative values for a nozzle on a ~100 kN thrust class engine.

| Parameter | Symbol | Value | Unit | Source | Confidence |
|-----------|--------|-------|------|--------|------------|
| Nozzle mass | m_n | TBD | kg | REF-030 | — |
| Nozzle moment of inertia (pitch axis) | I_n | TBD | kg·m² | REF-030 | — |
| CG-to-gimbal distance | l_cg | TBD | m | REF-030 | — |
| Maximum deflection | θ_max | ±6.0 | deg | PRF-001 | High |

---

## Flex Joint Parameters

| Parameter | Symbol | Value | Unit | Source | Confidence |
|-----------|--------|-------|------|--------|------------|
| Rotational stiffness | K_fj | TBD | Nm/rad | REF-031 | — |
| Rotational damping | C_fj | TBD | Nm·s/rad | ESTIMATE | Low |

**Notes:** Flex joint stiffness is a function of engine thrust class and joint geometry. Literature range: 500–5000 Nm/rad. Will be identified/tuned in Phase 3.

---

## Side-Load Environment

| Parameter | Symbol | Value | Unit | Source | Confidence |
|-----------|--------|-------|------|--------|------------|
| Peak side-load force | F_sl | TBD | N | REF-041/042 | — |
| Side-load frequency range | f_sl | 10–100 | Hz | REF-041 | Medium |
| Side-load spectral shape | — | Dryden model (shaped) | — | REF-040 | Medium |

---

## Summary of Phase 1 Actions

- [ ] Fill in all TBD motor parameters from literature (target: REF-002, REF-005)
- [ ] Fill in gearbox ratio and stiffness from literature (target: REF-002, REF-020)
- [ ] Estimate nozzle mass and MOI for a representative engine class (target: REF-030)
- [ ] Establish side-load magnitude estimate (target: REF-041, REF-042)
- [ ] Document all sources with page numbers / section references in this table
