# Grey-Box Building Thermal Modeling with CTSM-R (Single-Room + Multi-Room)

## Overview

This project presents a grey-box thermal modeling study of a university building using stochastic differential equations (SDEs) and the **CTSM-R / ctsmr** framework.

The work focuses on:

- **Part 2 (Single-room modeling):** building and improving a 2-state room thermal model with time-varying solar gain
- **Part 3 (Multi-room modeling):** extending the formulation to a coupled 6-state model for four rooms and two wall thermal masses

The project emphasizes **physical interpretability**, **residual diagnostics**, and **iterative model refinement**, rather than only optimizing predictive fit.

---

## Project Context

This repository is based on a course project from **Lund University** in advanced time series / stochastic modeling, where the goal was to build and improve grey-box thermal models for building temperature dynamics using CTSM-R.

Only the building thermal modeling components are included here (**Part 2 + Part 3**).  
(Part 1 from the original assignment is intentionally omitted in this repository.)

---

## Data and Variables

The building dataset includes indoor temperatures for four rooms and external driving variables such as:

- Indoor temperatures: `yTi1`, `yTi2`, `yTi3`, `yTi4`
- Ambient temperature: `Ta`
- Global solar radiation: `Gv`
- Heating power (north/south circuits): `Ph1`, `Ph2`

These variables are used to model heat transfer, solar gain, and heating effects in a physically interpretable SDE framework.

---

## Methods

## Part 2 — Single-Room Modeling (Room 1)

### 1) Baseline 2-state model
We started from a standard 2-state grey-box model for a single room:

- `Ti`: indoor air temperature
- `Tm`: thermal mass temperature

The baseline model assumes a **constant effective window area** (`Aw`) for solar gain. Residual analysis showed clear structure (especially around ~10:00 AM), indicating the model could not capture time-varying solar effects.

### 2) Time-varying solar gain with B-splines
To address this, the effective window area was modeled as a **time-varying function** using B-splines (5 basis functions during sun-active hours).

This improved flexibility, but the fitted model still produced:
- persistent residual instability around ~10:00 AM
- physically unrealistic estimates (e.g., inflated morning solar gain / effective window area)

### 3) Iterative refinement and model diagnostics
Several structural improvements were tested, including:

- increasing spline flexibility (more spline basis functions)
- redistributing solar heat between air and thermal mass

These attempts did **not** resolve the core mismatch and often introduced physical inconsistency or overfitting.

### 4) Hypothesis-driven improvement (“Student Effect”)
After inspecting the full temperature time series, we hypothesized that the unexplained morning behavior was caused by **unmodeled occupancy-related disturbances**, such as:

- human heat gains
- stochastic ventilation behavior (e.g., window opening)

To isolate building physics from human effects, we used a **two-stage strategy**:

#### Step 1 — Weekend calibration
A model was fitted using **weekend-only data** (lower occupancy impact), with a heater distribution parameter (`pph`) allowing radiator heat to affect both air and thermal mass.

This produced more stable residuals and physically plausible `R/C` estimates.

#### Step 2 — Fixed-physics full model + occupancy heat
The physical parameters identified in weekend calibration were then treated as fixed (or tightly constrained) priors for the full dataset.

A new **occupancy heat term** `Qocc(t)` was added (modeled with B-splines during school hours) to capture human-related thermal effects.

### 5) Overfitting control via measurement noise constraint
In the initial full model, the measurement noise parameter (`e11`) collapsed to an unrealistically low value, indicating overfitting.

To prevent this, we constrained the measurement noise to a realistic range. This improved parameter identifiability and yielded more physically consistent estimates, while keeping `Qocc` significant and interpretable.

---

## Part 3 — Multi-Room Modeling (6-state SDE extension)

### 1) Model structure
The multi-room extension models:

- 4 room air temperatures (`Ti1`–`Ti4`)
- 2 wall thermal masses (`Tw1`, `Tw2`)

The rooms are coupled through shared wall dynamics and inter-room heat transfer, and the model is estimated using **ctsmr + Extended Kalman Filter (EKF)**.

### 2) Initial issue: homogeneous solar gain
An initial model with a single solar gain coefficient (`Aw`) for all rooms led to:

- physically implausible thermal parameters
- identifiability issues
- near-zero estimated measurement noise (overfitting)

### 3) Refined model: split solar gains + constrained noise
To reflect building orientation, the solar term was split into:

- `Awn` for north-facing rooms
- `Aws` for south-facing rooms

Measurement noise was also constrained to prevent the optimizer from forcing sensor error to zero.

This significantly improved physical interpretability and residual behavior.

### 4) Key multi-room findings
The final model recovered clear and interpretable thermal structure, including:

- **Solar asymmetry:** south-facing solar gain (`Aws`) substantially larger than north-facing (`Awn`)
- **Thermal inertia:** wall-related dynamics dominate direct room-to-ambient exchange
- **Room coupling:** internal thermal coupling between room groups is significant

Residuals improved substantially overall, although Room 1 still showed a persistent daytime structural pattern, likely due to unmeasured occupancy/ventilation behavior and corner-room effects.

---

## Key Results and Takeaways

### Single-room (Part 2)
- Time-varying solar gain alone was **not sufficient** to explain the morning residual spike.
- The model initially “force-fit” solar gain (unrealistic window area / spline coefficients), indicating **missing dynamics**.
- A **weekend-calibration + fixed-physics + occupancy heat** strategy produced a much more robust and interpretable model.
- Constraining measurement noise improved identifiability and prevented overfitting.

### Multi-room (Part 3)
- Splitting solar gain by building orientation (`Awn` vs `Aws`) was essential for physical realism.
- The final 6-state model captured building envelope dynamics and room coupling well.
- Persistent residual structure in Room 1 suggests **information limitations** (occupancy and ventilation not directly measured), not necessarily model failure.

---

## My Contribution (Team Project)

This was a team project. My primary contribution was **Part 2 (single-room modeling and analysis)**, including:

- Building and modifying the 2-state CTSM-R model for Room 1
- Implementing time-varying solar gain using B-splines
- Performing residual diagnostics and identifying the ~10:00 AM structural mismatch
- Testing and analyzing multiple refinement attempts (spline flexibility, solar heat redistribution)
- Proposing and validating the occupancy-related (“Student Effect”) hypothesis
- Designing the two-stage modeling strategy (weekend calibration + fixed-physics full model)
- Interpreting parameter estimates and model diagnostics from a physical/statistical perspective

My teammate primarily contributed to **Part 3 (multi-room 6-state model design, estimation, and interpretation)**.

---

## Repository Structure

```text
.
├── README.md
├── data/
│   ├── README.md                  # data source / access notes
│   └── sample/                    # optional sample data (if public)
├── src/
│   ├── part2_single_room_baseline.R
│   ├── part2_single_room_spline_aw.R
│   ├── part2_weekend_calibration.R
│   ├── part2_full_model_qocc.R
│   ├── part3_multiroom_6state.R
│   └── utils_plotting.R
├── results/
│   ├── figures/
│   └── tables/
├── report/
│   └── project_report.pdf
└── docs/
    └── assignment_prompt.pdf      # optional
