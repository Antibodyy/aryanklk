---
title: "Two-Wheeled Balancing Robot (MPC Control)"
collection: projects
type: "Graduate Project"
permalink: /projects/balancing-robot
venue: "UC Berkeley"
location: "Berkeley, CA"
order: 5
---

Developed a high-performance self-balancing robotics system in the MuJoCo physics environment, implementing Model Predictive Control (MPC) for dynamic stabilization and trajectory tracking.

## System Architecture

The following flowchart illustrates the closed-loop interaction between the physical dynamics (simulated in MuJoCo) and the software control loop.

<figure class="project-figure" style="text-align: center; margin: 0 auto 20px auto; display: block;">
    <img src="{{ '/overall_arch.png' | relative_url }}" alt="System Architecture Flowchart" style="display: block; margin: 0 auto; max-width: 100%;">
    <figcaption style="text-align: center; margin-top: 8px;"><em>Figure 1: Overall hardware-software interaction architecture.</em></figcaption>
</figure>

## Performance & Simulation Results

All simulations were conducted within the MuJoCo environment to validate the MPC controller's response to dynamic conditions.

### 1. Autonomous Start and Stop
The robot demonstrates smooth acceleration and deceleration while maintaining vertical stability. The graph shows the high correlation between predicted velocity and actual performance.

<div style="display: flex; justify-content: center; gap: 20px; text-align: center; margin: 20px 0;">
    <figure class="project-figure" style="margin: 0; flex: 1;">
        <img src="{{ '/start_stop.gif' | relative_url }}" alt="Autonomous Start Stop Simulation" style="max-width: 100%; height: auto;">
        <figcaption style="margin-top: 8px;"><em>Autonomous Start/Stop Simulation</em></figcaption>
    </figure>
    <figure class="project-figure" style="margin: 0; flex: 1;">
        <img src="{{ '/drive_stop_prediction_acc.png' | relative_url }}" alt="Drive Stop Prediction Accuracy" style="max-width: 100%; height: auto;">
        <figcaption style="margin-top: 8px;"><em>Drive/Stop Prediction Accuracy</em></figcaption>
    </figure>
</div>

### 2. Perturbation Recovery
To test the robustness of the MPC controller, external forces were applied to the robot. The system successfully estimates the state deviation and applies corrective torque to return to an upright position.

<div style="display: flex; justify-content: center; gap: 20px; text-align: center; margin: 20px 0;">
    <figure class="project-figure" style="margin: 0; flex: 1;">
        <img src="{{ '/perturb_rec.gif' | relative_url }}" alt="Perturbation Recovery Simulation" style="max-width: 100%; height: auto;">
        <figcaption style="margin-top: 8px;"><em>Perturbation Recovery Simulation</em></figcaption>
    </figure>
    <figure class="project-figure" style="margin: 0; flex: 1;">
        <img src="{{ '/pitch_prediction_acc.png' | relative_url }}" alt="Pitch Prediction Accuracy Performance" style="max-width: 100%; height: auto;">
        <figcaption style="margin-top: 8px;"><em>Pitch Prediction Performance</em></figcaption>
    </figure>
</div>

### 3. Slope Balancing
The robot is capable of maintaining its center of gravity even when navigating inclined surfaces, showcasing the adaptability of the sensor fusion and state estimation algorithms.

<div style="display: flex; justify-content: center; gap: 20px; text-align: center; margin: 20px 0;">
    <figure class="project-figure" style="margin: 0; flex: 1;">
        <img src="{{ '/slope_bal.gif' | relative_url }}" alt="Slope Balancing Simulation" style="max-width: 100%; height: auto;">
        <figcaption style="margin-top: 8px;"><em>Slope Stability Simulation</em></figcaption>
    </figure>
    <figure class="project-figure" style="margin: 0; flex: 1;">
        <img src="{{ '/slope_stab_perf.png' | relative_url }}" alt="Slope Stability Performance Graph" style="max-width: 100%; height: auto;">
        <figcaption style="margin-top: 8px;"><em>Slope Stability Performance Graph</em></figcaption>
    </figure>
</div>

---

## My Contributions

* Designed and implemented a self-balancing robot using Model Predictive Control (MPC) algorithms for real-time dynamic stabilization.
* Integrated sensor fusion and state estimation to provide accurate feedback for the MPC controller, achieving stable performance under varying conditions.
* Executed complete hardware-software integration from sensor selection through control algorithm deployment in a simulated embedded robotics system.

## Technologies & Skills

* **Programming**: Python, C++, ROS2
* **Control Systems**: Model Predictive Control (MPC), State Estimation
* **Physics Engines**: MuJoCo
* **Robotics**: Sensor fusion, Embedded systems, Hardware-software integration

---

**Timeline**: August 2025 - December 2025