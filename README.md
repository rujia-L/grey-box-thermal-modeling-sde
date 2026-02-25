# Grey-Box Building Thermal Modeling with CTSM-R (Part 2–3)

## Overview
Grey-box thermal modeling of a university building using **CTSM-R / ctsmr** and **SDE state-space models**.  
This repository includes **Part 2 (single-room)** and **Part 3 (multi-room)** components of a course project at Lund University.

## Data
The dataset contains indoor temperatures (4 rooms) and exogenous drivers such as ambient temperature, solar radiation, and heating power.
See `data/README.md` for access notes and variable definitions.

## Part 2 — Single-Room Model
**Goal:** build an interpretable 2-state model for Room 1 and improve fit via diagnostics-driven refinement.

- Baseline **2-state** model: indoor air temperature (`Ti`) + thermal mass (`Tm`)  
- Added **time-varying solar gain** via **B-splines** for effective window area `Aw(t)`  
- Residual diagnostics revealed a persistent morning structural mismatch (~10:00), suggesting missing dynamics
- Proposed an **occupancy-related disturbance** (“Student Effect”) and implemented a **two-stage strategy**:
  1) **Weekend calibration** to identify physical parameters under low-occupancy conditions  
  2) Full-data model with fixed physics + spline-based **occupancy heat** term `Qocc(t)`
- Constrained measurement noise to avoid overfitting and improve identifiability

**Takeaway:** solar variation alone could not explain the morning bias; adding an occupancy disturbance produced a more stable and physically plausible model.

## Part 3 — Multi-Room Model 
**Goal:** extend to a coupled building model capturing room interactions.

- Built a **6-state** SDE model (4 rooms + 2 wall thermal masses) estimated with **EKF**
- Improved realism by splitting solar gain into **north vs south** parameters (`Awn`, `Aws`)
- Constrained measurement noise to prevent degenerate fits

**Key finding:** the model recovers clear solar asymmetry (`Aws > Awn`) and meaningful inter-room coupling, while Room 1 still shows residual structure likely due to unobserved occupancy/ventilation effects.

## Contribution
Team project:
- **Rujia Li led Part 2 (single-room modeling, diagnostics, refinement, and interpretation).**
- Xinxu Yao primarily contributed **Part 3 (multi-room 6-state model design/estimation).**

## Repo Structure
```text
R-code/        # modeling scripts (Part 2 & Part 3)
results/    # figures and tables
report/     # full report (details and additional plots)
Dataset/       # data notes (and sample data if allowed)
