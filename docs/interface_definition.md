# Interface Control Document — EMA/TVC Subsystem

**Document:** ICD-001  
**Revision:** A  
**Status:** Draft

---

## 1. Purpose

This document defines the signal interfaces at the boundary of the EMA/TVC subsystem model. It establishes what the flight computer sends to the EMA, what the EMA returns, and how those signals are represented in the simulation and Rust core.

---

## 2. Subsystem Boundary Diagram

```
                    ┌─────────────────────────────────────────────┐
                    │              EMA / TVC Subsystem             │
                    │                                              │
 cmd_position_deg ─►│─► Motor Drive ─► BLDC Motor ─► Gearbox ──► │─► nozzle_angle_deg
                    │                                    │         │
  enable_flag     ─►│                             Flex Joint       │
  phase_id        ─►│                                    │         │
                    │◄──────── Position Encoder ──────────        │
                    │                                              │
                    │  ┌─────────────────────────────┐            │
                    │  │   FDIR State Machine         │            │
                    │  └─────────────────────────────┘            │
                    └─────────────────────────────────────────────┘
                              │            │           │
                     pos_feedback   fault_word    telemetry
```

---

## 3. Input Signals

| Signal | Type | Units | Rate | Range | Description |
|--------|------|-------|------|-------|-------------|
| `cmd_position_deg` | f64 | degrees | 100 Hz | [-6.0, 6.0] | Nozzle position command from flight computer |
| `enable_flag` | bool | — | 100 Hz | {true, false} | EMA enable. False = hold last position and inhibit motion |
| `phase_id` | u8 | — | 1 Hz (or on change) | [1, 5] | Current operational phase (maps to OP-1 through OP-5) |
| `v_bus` | f64 | Volts | 10 Hz | [24.0, 48.0] | Bus voltage supplied to motor drive |

---

## 4. Output Signals

| Signal | Type | Units | Rate | Range | Description |
|--------|------|-------|------|-------|-------------|
| `nozzle_angle_deg` | f64 | degrees | 100 Hz | [-6.5, 6.5] | Measured nozzle position (encoder output) |
| `nozzle_rate_dps` | f64 | deg/s | 100 Hz | [-30.0, 30.0] | Nozzle angular rate (encoder derived) |
| `motor_current_a` | [f64; 3] | Amps | 100 Hz | [-50.0, 50.0] each | Phase currents: [I_a, I_b, I_c] |
| `motor_speed_rpm` | f64 | RPM | 100 Hz | [-6000, 6000] | Motor shaft speed |
| `winding_temp_c` | f64 | °C | 10 Hz | [-40.0, 200.0] | Estimated motor winding temperature |
| `fault_word` | FaultWord | — | 100 Hz | — | See Section 5 |
| `validity_flags` | ValidityWord | — | 100 Hz | — | See Section 6 |

---

## 5. Fault Word Definition

The `FaultWord` is a structured type (serialized as u32 for wire representation) containing:

```
Bit [0]   — PHASE_LOSS_DETECTED
Bit [1]   — MECHANICAL_JAM_DETECTED
Bit [2]   — ENCODER_DROPOUT_DETECTED
Bit [3]   — THERMAL_OVERTEMP_DETECTED
Bit [4]   — RUNAWAY_DETECTED
Bit [5]   — BACKLASH_DEGRADATION_DETECTED
Bit [6]   — SAFE_MODE_ACTIVE
Bit [7]   — LIMP_HOME_ACTIVE
Bits[8:15] — FAULT_SEVERITY (0=none, 1=advisory, 2=caution, 3=warning, 4=critical)
Bits[16:31] — Reserved
```

In Rust this is modeled as a typed struct with bitfield access, not a raw integer, to prevent misinterpretation.

---

## 6. Validity Word Definition

```
Bit [0]   — CMD_POSITION_VALID        (in range and rate-of-change within limits)
Bit [1]   — NOZZLE_ANGLE_VALID        (encoder signal healthy)
Bit [2]   — MOTOR_CURRENT_VALID       (all three phases reporting)
Bit [3]   — WINDING_TEMP_VALID        (thermal model converged)
Bit [4]   — ENABLE_FLAG_VALID         (not stale)
```

---

## 7. Rust Type Definitions (Reference)

```rust
/// Input command packet from flight computer to EMA
#[derive(Debug, Clone, serde::Serialize, serde::Deserialize)]
pub struct EmaCommand {
    pub cmd_position_deg: f64,
    pub enable_flag: bool,
    pub phase_id: OperationalPhase,
    pub v_bus: f64,
}

/// Output telemetry packet from EMA to flight computer
#[derive(Debug, Clone, serde::Serialize, serde::Deserialize)]
pub struct EmaTelemetry {
    pub nozzle_angle_deg: f64,
    pub nozzle_rate_dps: f64,
    pub motor_current_a: [f64; 3],
    pub motor_speed_rpm: f64,
    pub winding_temp_c: f64,
    pub fault_word: FaultWord,
    pub validity_flags: ValidityWord,
    pub timestamp_s: f64,
}

/// Operational phase identifier
#[derive(Debug, Clone, Copy, PartialEq, serde::Serialize, serde::Deserialize)]
pub enum OperationalPhase {
    PreIgnitionCheckout = 1,
    IgnitionStartupTransient = 2,
    Mainstage = 3,
    ThrottleTransient = 4,
    Shutdown = 5,
}
```

---

## 8. Simulation Harness Interface

The Python simulation harness interacts with the Rust core via PyO3. The harness is responsible for:

- Generating `EmaCommand` structs at 100 Hz
- Injecting side-load disturbances into the plant model
- Injecting fault stimuli for FDIR testing
- Collecting `EmaTelemetry` for logging and plotting
- Advancing simulation time

The Rust core exposes a single `step(cmd: EmaCommand, dt: f64) -> EmaTelemetry` function as the primary simulation interface.

---

## 9. Open Items

| Item | Notes |
|------|-------|
| v_bus range | Depends on power system architecture choice — placeholder 24–48V |
| Phase current range | Depends on motor sizing — TBD in Phase 2 |
| Encoder resolution | TBD from literature — expected 12–16 bit |
