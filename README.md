# EV Regenerative Braking System Simulation

A high-fidelity MATLAB/Simulink simulation of an Electric Vehicle (EV) powertrain showcasing bidirectional power flow, synchronous half-bridge DC-DC converter topology, and closed-loop PI current control for optimal energy recovery.

## Project Overview

In modern EVs, efficiency is heavily driven by capturing kinetic energy during deceleration — regenerative braking. This project models the complete electrical and mechanical transition phases of an EV drivetrain. A synchronous half-bridge DC-DC converter lets the system seamlessly transition between Quadrant I (motoring/acceleration) and Quadrant II (generating/braking), with a closed-loop PI controller ensuring stable current tracking and protecting BMS limits.

## System Architecture

<img width="1316" height="712" alt="image" src="https://github.com/user-attachments/assets/29bd2838-9bd6-4b35-ae4c-6baeb1e5cf39" />

### 1. Energy Storage System (ESS)
- Lithium-Ion battery block: 48V nominal, 100 Ah rated capacity
- Acts as energy source during motoring, dynamic load/sink during regen braking

### 2. Bidirectional DC-DC Power Stage (Synchronous Half-Bridge)
- 2x MOSFETs (S1, S2) with anti-parallel diodes
- 1 mH inductor, 100 µF capacitor
- **Buck mode (acceleration):** S1 driven by 20 kHz PWM, S2 OFF — steps down battery voltage to drive the motor
- **Boost mode (regen braking):** S1 OFF, S2 pulsed — inductor stores energy from back-EMF, delivers boosted voltage that pushes current back into the battery

### 3. Drivetrain / Actuator
- DC machine block (permanent magnet field) — simulates the motor, switching to generator mode under braking torque

## Control Loop Design

- Switching frequency: 20 kHz
- Feedback: current measurement in series with the inductor
- Error signal: braking reference current (-10 A) minus measured inductor current
- PI controller with anti-windup, duty cycle saturation locked between 0.05–0.95 to prevent shoot-through faults
- Complementary gate logic (NOT gate) ensures S1 and S2 never conduct simultaneously

## Simulation Results
<img width="956" height="940" alt="final_scope" src="https://github.com/user-attachments/assets/3e7c546b-e04c-4a64-9ad7-2065738f6c24" />


Simulated for T = 2.0 s, discrete solver step size 1e-5 s (powergui).

**Motoring phase (0–1 s):** inrush current spike overcomes rotor inertia, current stabilizes positive, speed ramps up smoothly, power flows out of the battery.

**Braking event (t = 1 s):** step change in load torque triggers the transition; motor's angular momentum generates back-EMF as gate control shifts.

**Regenerative recovery (1–2 s):** armature current inverts polarity and settles at the -10 A reference target, confirming power flow back into the battery. Torque mirrors the current, providing counter-rotational braking force. No post-transition oscillation, validating PI tuning stability.

## Files

| File | Description |
|---|---|
| `ev_model.slx` | Main Simulink model |
| `results/` | Circuit diagram and scope waveform screenshots |

## Tech Stack
- MATLAB R2024b / R2025a
- Simulink, Simscape Electrical (Specialized Power Systems)
- Power electronics converter design, closed-loop control systems, machine modeling

## How to Run
1. Clone this repository
2. Open MATLAB and navigate to the project folder
3. Open `ev_model.slx`
4. Ensure powergui is set to Discrete (1e-5 s)
5. Click Run, double-click the Scope block to inspect waveforms
