# System Sketch & Equations of Motion — Phase 1

**Document:** EOM-001  
**Revision:** A  
**Status:** Draft — complete derivations during Phase 1 before coding Phase 2

---

## 1. System Decomposition

The EMA/TVC system is decomposed into four coupled subsystems:

```
[Motor Drive] → [BLDC Motor] → [Gearbox] → [Nozzle + Flex Joint]
                     ↑                              ↓
               [Voltage cmd]               [Side-load disturbance]
                     ↑                              ↓
               [Current loop]          [Position feedback (encoder)]
```

---

## 2. BLDC Motor Model (Electrical)

Using a simplified d-q axis model reduced to a single-axis equivalent for the position control application:

**Electrical dynamics (per phase equivalent):**

```
L * (dI/dt) = V_applied - R*I - K_e * ω_m

where:
  I       = motor current (A)
  V_applied = applied voltage from drive (V)
  R       = winding resistance (Ω)
  L       = winding inductance (H)
  K_e     = back-EMF constant (V·s/rad)
  ω_m     = motor shaft angular velocity (rad/s)
```

**Torque equation:**

```
τ_motor = K_t * I

where:
  τ_motor = motor output torque (Nm)
  K_t     = torque constant (Nm/A)
  K_t     = K_e  (in SI units — verify this numerically)
```

---

## 3. Motor Mechanical Dynamics

```
J_m * (dω_m/dt) = τ_motor - B * ω_m - τ_load_reflected

where:
  J_m              = rotor inertia (kg·m²)
  B                = viscous friction (Nm·s/rad)
  τ_load_reflected = load torque reflected through gearbox = τ_output / (N * η)
  N                = gear ratio (dimensionless)
  η                = gearbox efficiency
```

---

## 4. Gearbox Model

**Phase 2 (rigid, no compliance):**

```
θ_output = θ_m / N
ω_output = ω_m / N
τ_output = τ_motor * N * η
```

**Phase 4 extension — torsional compliance + backlash:**

```
Backlash model (deadband):
  θ_deflection = θ_output - θ_nozzle
  
  if |θ_deflection| > δ/2:
    τ_transmitted = K_g * (|θ_deflection| - δ/2) * sign(θ_deflection) + C_g * ω_rel
  else:
    τ_transmitted = 0   (deadband — no torque transmitted)

where:
  δ     = total backlash deadband (rad)
  K_g   = gearbox torsional stiffness (Nm/rad)
  C_g   = gearbox torsional damping (Nm·s/rad)
  ω_rel = relative angular velocity across gearbox compliance
```

---

## 5. Nozzle + Flex Joint Dynamics

**Rigid body rotation about gimbal point:**

```
I_n * (d²θ_n/dt²) = τ_transmitted - K_fj * θ_n - C_fj * (dθ_n/dt) + F_sl * l_cg

where:
  I_n   = nozzle moment of inertia (kg·m²)
  θ_n   = nozzle deflection angle (rad)
  K_fj  = flex joint rotational stiffness (Nm/rad)
  C_fj  = flex joint rotational damping (Nm·s/rad)
  F_sl  = side-load force (N)
  l_cg  = moment arm from gimbal to nozzle CG (m)
```

**Note:** This is a 1-DOF model per axis (pitch and yaw are decoupled in Phase 2). Phase 4 may introduce cross-coupling.

---

## 6. State Vector Definition

For Phase 2 implementation, the state vector is:

```
x = [I, ω_m, θ_n, dθ_n/dt]^T

  x[0] = I         Motor current (A)
  x[1] = ω_m       Motor angular velocity (rad/s)
  x[2] = θ_n       Nozzle angle (rad)
  x[3] = dθ_n/dt   Nozzle angular rate (rad/s)
```

Control input:

```
u = V_applied   (Volts — command from motor drive)
```

Disturbance input:

```
d = F_sl   (Side-load force, N)
```

Output (measured):

```
y = θ_n   (Nozzle angle from encoder, rad)
```

---

## 7. State-Space Form (Phase 2, Linear)

The linearized continuous-time state-space form (for LQR design in Phase 5):

```
dx/dt = A*x + B*u + Bd*d
y     = C*x + D*u

A = [ -R/L        -K_e/L        0              0      ]
    [ K_t/J_m     -B/J_m        -N*K_fj/J_m   -N*C_fj/J_m ]  ← approximate for rigid gearbox
    [  0           1/N           0              1      ]
    [  0           0            -K_fj/I_n      -C_fj/I_n   ]

B  = [1/L, 0, 0, 0]^T

Bd = [0, 0, 0, l_cg/I_n]^T

C  = [0, 0, 1, 0]

D  = 0
```

**Note:** This linearization assumes rigid gearbox and small-angle nozzle deflection. Populate with actual parameter values in Phase 2 after filling `parameter_estimates.md`.

---

## 8. Thermal Model (Phase 4)

Motor winding temperature as a first-order thermal system:

```
C_th * (dT_w/dt) = I² * R - (T_w - T_case) / R_th

where:
  T_w    = winding temperature (°C)
  T_case = case/ambient temperature (°C) — assumed constant for Phase 4
  C_th   = winding thermal capacitance (J/°C)
  R_th   = winding-to-case thermal resistance (°C/W)
  I²*R   = resistive heating power (W)
```

---

## 9. Phase 2 Implementation Notes

- Use `scipy.integrate.solve_ivp` with RK45 for time integration
- Timestep: 1 ms (1000 Hz) for simulation, downsampled to 100 Hz for controller
- All angles in radians internally, convert to degrees for display and I/O
- Implement each subsystem as a Python class with a `step(inputs, dt)` method
- Keep motor, gearbox, and nozzle as separate classes — compose in `EmaPlant`
- Side-load: start with `F_sl = A * sin(2π * f * t)` — parameterize A and f

---

## 10. Open Derivation Items (Complete Before Phase 2)

- [ ] Verify K_t = K_e numerically with chosen motor parameters
- [ ] Work through units on full state-space matrix — confirm dimensional consistency
- [ ] Determine appropriate gearbox ratio N for the chosen thrust class
- [ ] Sketch free-body diagram of nozzle about gimbal point (hand drawing, scan or draw in ASCII)
- [ ] Decide: model pitch and yaw independently (decoupled 1-DOF each) or as coupled 2-DOF?
