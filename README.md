# Hardware Validation and Comparative Controller Benchmarking of a Rotary Inverted Pendulum

**Group 17 | EE302 Control Systems Laboratory (PS2) — IIT Mandi**

Real-time hardware-in-the-loop (HIL) validation and benchmarking of **Classical LQR** and **Robust $H_\infty$** controllers on the **Quanser QUBE-Servo 3** rotary inverted pendulum at **500 Hz** ($T_s = 0.002\text{ s}$) using MATLAB, Simulink, and QUARC.

## Team Members (Group 17)
* **Shreyansh Raj** (B24284)
* **Shubham Kumar** (B24285)
* **Subh Kashyap** (B24286)
* **T. Monica** (B24287)

---

## Project Overview

Stabilizing a rotary inverted pendulum from its hanging position requires solving two distinct control problems:
1. **Nonlinear Energy Swing-Up:** Pumping energy into the pendulum to reach the upright capture basin without inducing chattering or arm drift.
2. **Upright Stabilization & Disturbance Rejection:** Balancing an open-loop unstable, underactuated 2-DOF system (unstable pole at $+9.95\text{ s}^{-1}$) under encoder quantization, friction ($0.3\text{ V}$ stiction), and $\pm 10\text{ V}$ actuator limits.

### Key Engineering Contributions
* **Four-Stage Swing-Up Refinement:** Eliminated a $2\text{--}3\text{ s}$ oscillatory capture transient via:
  1. $5\%$ energy overdrive ($E_r = 30.37\text{ mJ} \rightarrow E_{\text{ref}} = 31.89\text{ mJ}$)
  2. Smooth rational saturation switching $s / (|s| + 0.05)$ replacing the discontinuous `sign()` relay
  3. Swing-up arm-centering PD feedback ($K_C = 0.5\text{ V/rad}$, $K_D = 0.05\text{ V}\cdot\text{s/rad}$)
  4. Hysteresis mode-switching thresholds ($\epsilon_{\text{in}} = 0.35\text{ rad}$, $\epsilon_{\text{out}} = 0.60\text{ rad}$)
* **Open-Loop Calibration & HIL Safety:** Verified actuator polarity in `open_loop.slx` ($\pm 5.0\text{ V}$ clamp), calibrated upright encoder trim ($\alpha_{\text{trim}} = 0.007\text{ rad}$), applied a $50\text{ rad/s}$ causal washout velocity filter, and enforced a $\pm 10\text{ V}$ hardware safety clamp.
* **LQR vs. $H_\infty$ Implementation:**
  * **LQR (`closed_loop.slx`):** Full-state feedback with $Q = \text{diag}(5, 30, 1, 1)$, $R = 1.0$, yielding $K_{\text{lqr}} = [-2.2361,\; -55.6132,\; -2.0475,\; -4.1637]$.
  * **$H_\infty$ (`hinf_controller.slx`):** 6th-order discrete-time dynamic controller ($500\text{ Hz}$) with bumpless reference transfer (`REF_RATE = 0.3 rad/s`) and an outer arm-centering PD compensator ($K_{c,\text{arm}} = 2.0\text{ V/rad}$, $K_{d,\text{arm}} = 0.5\text{ V}\cdot\text{s/rad}$).

---

## Hardware Benchmarking Results (22 Logged Runs)

| Performance Metric | Classical LQR | Robust $H_\infty$ | Key Takeaway |
| :--- | :---: | :---: | :--- |
| **Swing-Up Capture (3/3 trials each)** | $100\%$ in $0.664\text{ s}$ | $100\%$ in $0.590\text{ s}$ | Both achieve $100\%$ capture |
| **Max Catch Angle (ROA)** | $29.48^\circ$ | **$36.51^\circ$** | $H_\infty$ has a wider Region of Attraction |
| **Tap Disturbance Recovery** | **$0.460\text{ s}$** | $2.365\text{ s}$ | LQR recovers **$5\times$ faster** |
| **Peak Arm Deflection (Tap)** | **$46.67^\circ$** | $108.16^\circ$ | LQR constrains arm motion tighter |
| **Quiet Balance Precision ($\alpha_{\text{rms}}$)** | **$0.221^\circ$** | $0.773^\circ$ | LQR achieves **$3.5\times$ tighter** regulation |
| **High-Freq Voltage Noise ($>20\text{ Hz}$)** | $52.23\%$ | **$5.84\%$** | $H_\infty$ has **$9\times$ lower** HF control effort |

---

## Repository Structure

```text
├── pendulam_setup.m                              # Plant parameters, linearized model, LQR & swing-up gains
├── open_loop.slx                                 # Open-loop polarity, trim, and stiction verification
├── closed_loop.slx                               # Swing-up + LQR balance controller (500 Hz QUARC)
├── hinf_controller.slx                           # Swing-up + 6th-order discrete H-infinity controller
├── data/                                         # 22 hardware .mat logs (swing-up, ROA, disturb, quiet, ablation)
├── plots/                                        # 23 comparison and validation .png figures
├── report/
│   └── G17_EE302_Final_Report.tex                # 13-page Overleaf LaTeX technical report
└── slides/
    └── Hardware-Validation-and-Comparative-Controller-Benchmarking-of-a-Rotary-Inverted-Pendulum.pdf
```

## Tech Stack
`MATLAB` · `Simulink` · `Quanser QUARC` · `QUBE-Servo 3` · `LQR` · `H-infinity Control` · `LaTeX`
