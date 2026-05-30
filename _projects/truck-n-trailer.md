---
title: "Truck-N-Trailer Autonomous Parking System"
collection: projects
type: "Academic Project"
permalink: /projects/truck-n-trailer
venue: "UC Berkeley — ME 235"
location: "Berkeley, CA"
order: 4
---

Built a full-stack embedded control system that autonomously parks a physical truck-trailer model using nonlinear MPC, overhead computer vision, and hard real-time FreeRTOS firmware.

## Project Overview

Backing up a truck with a trailer is one of the most challenging vehicle control tasks — a nonholonomic problem where preventing jackknife takes experienced drivers years to master. This project constructs a scaled physical model of the truck-trailer system and solves the autonomous parking problem end-to-end: from motor control on embedded hardware, through a live overhead vision pipeline, up to a nonlinear model predictive controller running on a laptop.

<figure style="text-align: center; margin: 0 auto 30px auto; display: block; width: 100%;">
    <img src="{{ '/tnt_assembly.png' | relative_url }}" alt="Truck-N-Trailer physical model" style="max-width: 90%; height: auto; display: inline-block; border-radius: 8px; border: 1px solid #eee;">
    <figcaption style="margin-top: 10px; color: #666;"><em>The physical Truck-N-Trailer model — 3D-printed chassis with ESP32 embedded controller, quadrature encoders, and ArUco fiducial markers for overhead tracking.</em></figcaption>
</figure>

The system supports two modes: **Manual Mode**, where the user drives with on-screen controls, and **Automatic Mode**, where the MPC planner takes over and parks the trailer autonomously.

---

## System Architecture

The system spans two computing layers with fundamentally different timing requirements.

### ESP32 — Hard Real-Time (FreeRTOS)

The ESP32 runs two concurrent FreeRTOS tasks:

* **Control Task (100 Hz, higher priority):** Triggered at a fixed interval by a GPTimer ISR via `vTaskNotifyGiveFromISR`. Reads quadrature encoder deltas, converts counts to RPM, runs independent PID controllers for left and right wheels, applies a static feedforward term, and drives the motors via PWM. Sends telemetry at 10 Hz. A stale-command timeout automatically falls back to zero RPM as a safety measure.

* **Communication Task (lower priority):** Triggered by the UART receive interrupt. Parses incoming target RPM values and writes them to shared state protected by a FreeRTOS mutex. Reads the hitch-angle potentiometer ADC value and forwards it in the telemetry stream.

Interrupt sources include four hardware encoder channel ISRs (quadrature A/B for each motor), an emergency stop GPIO interrupt, and the GPTimer alarm ISR that unblocks the Control Task at exactly 100 Hz.

### Laptop — Soft Real-Time (Python / PyQt6)

The laptop side runs two `QTimer` callbacks within a single PyQt6 event loop:

* **Camera timer (30 Hz):** Grabs a frame from the overhead camera, runs ArUco marker detection to locate the truck (ID 0), trailer (ID 1), and goal (ID 10), and computes world-frame poses — producing the vehicle state vector `vision_q` and goal position `goal_xy`.

* **Send timer (10 Hz):** Polls the UART receive buffer for the latest telemetry, runs the MPC solver if in autonomous mode, and writes the resulting RPM targets back over UART. The IPOPT solver typically converges in 50–200 ms with a warm start from the previous solution.

All data and command communication between layers occurs over UART at 115,200 baud.

| Component | Rate | Deadline type | Dominant cost |
|---|---|---|---|
| ESP32 PID loop | 100 Hz | Hard | GPTimer ISR latency (<1 µs) |
| ESP32 telemetry TX | 10 Hz | Soft | UART byte write (~1 ms) |
| Laptop camera tick | 30 Hz | Soft | ArUco detection (10–25 ms) |
| Laptop send tick | 10 Hz | Soft | MPC solve (50–200 ms) |

---

## Graphical User Interface

The PyQt6 GUI serves as the operator dashboard and is the interface between the human, the vision pipeline, the MPC planner, and the firmware.

<figure style="text-align: center; margin: 0 auto 30px auto; display: block; width: 100%;">
    <img src="{{ '/tnt_gui_boot.png' | relative_url }}" alt="GUI boot sequence" style="max-width: 90%; height: auto; display: inline-block; border-radius: 8px; border: 1px solid #eee;">
    <figcaption style="margin-top: 10px; color: #666;"><em>Boot sequence overlay — initializes and confirms all subsystems before the dashboard loads.</em></figcaption>
</figure>

<figure style="text-align: center; margin: 0 auto 30px auto; display: block; width: 100%;">
    <img src="{{ '/tnt_gui_start.png' | relative_url }}" alt="GUI main dashboard" style="max-width: 90%; height: auto; display: inline-block; border-radius: 8px; border: 1px solid #eee;">
    <figcaption style="margin-top: 10px; color: #666;"><em>Main dashboard — live overhead camera feed with ArUco bounding boxes and planned trajectory overlay, dual hitch-angle dials, telemetry cards, and manual drive controls.</em></figcaption>
</figure>

Key GUI features:
* **Dual hitch-angle dials**: the left dial reads the physical potentiometer off the ESP32; the right dial is computed from the ArUco marker positions by the vision pipeline — allowing real-time cross-validation.
* **Calibration wizard**: maps raw ADC counts to degrees using a piecewise-linear fit at four known hitch angles (max negative, zero, max positive, zero again to capture hysteresis). Required before autonomous operation.
* **Live camera overlay**: draws bounding boxes for the truck, trailer, and parking goal; shows the MPC-planned trajectory in Automatic Mode.
* **Telemetry cards**: display actual wheel RPMs, commanded speeds, and UART connection status at 10 Hz.

<figure style="text-align: center; margin: 0 auto 30px auto; display: block; width: 100%;">
    <img src="{{ '/tnt_gui_sim.gif' | relative_url }}" alt="GUI in action" style="max-width: 90%; height: auto; display: inline-block; border-radius: 8px; border: 1px solid #eee;">
    <figcaption style="margin-top: 10px; color: #666;"><em>GUI boot and full workflow — subsystem initialization, camera connection, and transition into Automatic Mode.</em></figcaption>
</figure>

---

## Nonlinear Model Predictive Controller

The high-level path planner is a Nonlinear MPC (NMPC) implemented in Pyomo and solved with IPOPT at 10 Hz. At each decision step it optimizes a finite horizon of future motion.

**State vector:** `x = [x, y, θ_t, θ_l, v, ω]ᵀ`

where `(x, y)` is the truck rear-axle position, `θ_t` and `θ_l` are the truck and trailer headings, and `v` and `ω` are linear and angular velocity. Control inputs are linear acceleration `a` and angular acceleration `α`.

The dynamics constraints use discrete-time Euler integration of the truck-trailer equations: the truck advances along its heading, headings integrate from angular velocity, and the trailer heading evolves from the hitch geometry. The key safety constraint is the **jackknife prevention bound** — enforcing `|θ_t − θ_l| ≤ θ_max` throughout the horizon. The objective is a weighted sum of squared errors penalizing terminal pose error, intermediate goal proximity, and control effort.

---

## Simulations

<figure style="text-align: center; margin: 0 auto 30px auto; display: block; width: 100%;">
    <img src="{{ '/tnt_backwards_sim.gif' | relative_url }}" alt="Autonomous reverse parking simulation" style="max-width: 90%; height: auto; display: inline-block; border-radius: 8px; border: 1px solid #eee;">
    <figcaption style="margin-top: 10px; color: #666;"><em>Autonomous reverse parking — MPC drives the trailer backward into the target spot while enforcing the jackknife constraint.</em></figcaption>
</figure>

<figure style="text-align: center; margin: 0 auto 30px auto; display: block; width: 100%;">
    <img src="{{ '/tnt_forward_correcting_sim.gif' | relative_url }}" alt="Forward correcting simulation" style="max-width: 90%; height: auto; display: inline-block; border-radius: 8px; border: 1px solid #eee;">
    <figcaption style="margin-top: 10px; color: #666;"><em>Forward motion with active steering correction — MPC continuously replans as the truck-trailer state deviates from the nominal trajectory.</em></figcaption>
</figure>

<figure style="text-align: center; margin: 0 auto 30px auto; display: block; width: 100%;">
    <img src="{{ '/tnt_forward_straight_sim.gif' | relative_url }}" alt="Forward straight simulation" style="max-width: 90%; height: auto; display: inline-block; border-radius: 8px; border: 1px solid #eee;">
    <figcaption style="margin-top: 10px; color: #666;"><em>Straight-line forward simulation — baseline maneuver validating speed tracking and differential motor PID.</em></figcaption>
</figure>

---

## Hardware

<div style="display: flex; gap: 20px; justify-content: center; margin-bottom: 30px; flex-wrap: wrap;">
    <figure style="text-align: center; flex: 1; min-width: 280px; max-width: 48%;">
        <img src="{{ '/tnt_hitch.png' | relative_url }}" alt="Hitch potentiometer" style="width: 100%; height: auto; border-radius: 8px; border: 1px solid #eee;">
        <figcaption style="margin-top: 8px; color: #666;"><em>Potentiometer-based hitch angle detection at the truck-trailer joint.</em></figcaption>
    </figure>
    <figure style="text-align: center; flex: 1; min-width: 280px; max-width: 48%;">
        <img src="{{ '/tnt_wiring.png' | relative_url }}" alt="Core wiring and electronics" style="width: 100%; height: auto; border-radius: 8px; border: 1px solid #eee;">
        <figcaption style="margin-top: 8px; color: #666;"><em>ESP32, breadboard, motor drivers, and quadrature encoder wiring on the truck chassis.</em></figcaption>
    </figure>
</div>

---

## Technologies & Skills

* **Embedded Systems**: C, FreeRTOS (tasks, mutexes, GPTimer ISR, UART IRQ), ESP32, PWM motor control
* **Computer Vision**: Python, OpenCV, ArUco fiducial markers, world-frame pose estimation
* **Control Theory**: Nonlinear MPC, Pyomo/IPOPT, PID, jackknife constraint formulation, quadrature encoder ISR
* **Software**: PyQt6 event-driven GUI, real-time UART telemetry, piecewise-linear calibration
* **Hardware**: 3D-printed chassis, DC motors with quadrature encoders, potentiometer hitch sensor, overhead camera rig

---

**Team**: RealTime Rebels (Eric Chuang, Evan Grealish, Aryan Kulkarni, Xiao Liu)  
**Timeline**: Spring 2026 — ME 235, UC Berkeley
