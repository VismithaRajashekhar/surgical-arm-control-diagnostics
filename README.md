#  RBA-2201 Surgical Arm Control Optimization
### Johnson & Johnson MedTech — Robotics & Controls Engineering

> **Internship Project** | Diagnosing and resolving response delays in a surgical robotic arm (Model RBA-2201), followed by a full hardware design optimization proposal.

---

##  Project Overview

This repository documents the complete engineering workflow for **Ticket #2437** — a high-priority issue reported by Dr. Emily Chen (Senior Surgeon) at Mercy General Hospital, Rockville, Maryland. The `rotate_joint` command on the RBA-2201 surgical robotic arm was exhibiting response times of **0.35 seconds**, nearly double its 0.18-second target threshold, posing a direct risk to surgical precision.

The project is split into two tasks:

| Task | Focus | Outcome |
|------|-------|---------|
| **Task 1** | Diagnostics & code fix | Identified 3 software inefficiencies; reduced latency to 0.17 s |
| **Task 2** | Hardware design optimization | Proposed 3 modifications; projected latency of 0.16 s (54% improvement) |

---

##  Repository Structure

```
surgical-arm-control-diagnostics/
│
├── README.md                          # This file
│
├── notebooks/
│   └── control_system_diagnostics.ipynb   # Python diagnostic notebook (Task 1 & 2)
│
├── scripts/
│   └── response_time_checker.py           # Standalone diagnostic script
│
├── reports/
│   ├── Diagnostic_Report_Ticket2437.docx  # Task 1 — Diagnostic report
│   └── Design_Proposal_RBA2201_Task2.docx # Task 2 — Design optimization proposal
│
└── docs/
    ├── task1_summary.md               # Task 1 written summary
    └── task2_summary.md               # Task 2 written summary
```

---

##  Task 1 — Control System Diagnostics

### Problem
The `rotate_joint` command was clocking **0.35 s** against a 0.18 s target — a 94% excess. `move_arm` (0.10 s) and `adjust_grip` (0.09 s) were both within expected ranges.

### Root Causes Identified
1. **Redundant recalculation loop** — Angular offset recomputed on every cycle instead of being cached
2. **Synchronous validation bottleneck** — A blocking validation subroutine adding ~0.12–0.15 s per call
3. **Excessive conditional branching** — 8-branch position verification adding ~0.04–0.07 s overhead

### Fix Applied
- Eliminated redundant loop; cached angular offset value
- Refactored validation to run asynchronously on a separate thread
- Consolidated conditional branches from 8 to 3

### Result
`rotate_joint` post-fix: **0.17 s**  (within 0.18 s threshold), validated over 10 consecutive test runs.

 **Full report:** [`reports/Diagnostic_Report_Ticket2437.docx`](reports/Diagnostic_Report_Ticket2437.docx)

---

##  Task 2 — Design Optimization Proposal

### Three Hardware Modifications Proposed

#### Modification A — Joint Assembly Material Upgrade
- Replace steel sleeve with **Ti-6Al-4V titanium alloy** housing (40% mass reduction)
- Apply **medical-grade PTFE coating** to bearing surfaces (friction: 0.42 → 0.27)
- Add sealed self-lubricating reservoir for maintenance-interval stability

#### Modification B — Dual-Actuator Split-Drive Architecture
- Replace single-actuator drive with **two matched brushless DC motors**
- Implement **differential torque controller** for dynamic load balancing
- Actuator load variance: 38% → 17%

#### Modification C — Sensor Relocation & Dynamic Recalibration
- Relocate encoders from 28 mm to **6 mm from joint pivot** (79% shorter signal path)
- Upgrade from 12-bit incremental to **16-bit absolute encoder** (0.0055° resolution)
- Implement **100 Hz dynamic recalibration** in real-time OS thread

### Simulation Results (Combined A+B+C)

| Metric | Baseline | Combined |
|--------|----------|----------|
| rotate_joint latency | 0.35 s | **0.16 s** |
| Joint friction coefficient | 0.42 | **0.27** |
| Actuator load variance | 38% | **17%** |
| Sensor feedback delay | 48 ms | **31 ms** |
| System reliability score | 71/100 | **94/100** |

 **Full proposal:** [`reports/Design_Proposal_RBA2201_Task2.docx`](reports/Design_Proposal_RBA2201_Task2.docx)

---

##  Running the Diagnostic Notebook

### Requirements
```bash
pip install numpy scipy matplotlib jupyter
```

### Launch
```bash
jupyter notebook notebooks/control_system_diagnostics.ipynb
```

The notebook is structured into cells that can be run independently:
- **Cell 1–3**: Setup and command definitions
- **Cell 4–6**: `check_response_time` baseline measurements
- **Cell 7–9**: Code optimization and re-testing
- **Cell 10–12**: SciPy/NumPy simulation for hardware modifications

### Standalone Script
```bash
python scripts/response_time_checker.py
```

---

##  Tools & Technologies

- **Python 3.x** — Diagnostics and simulation
- **NumPy / SciPy** — Numerical computation and performance modelling
- **Matplotlib** — Response time visualization
- **Jupyter Notebook** — Interactive diagnostic environment

---

##  Key References

- Ticket ID: **#2437**
- Facility: Mercy General Hospital, Rockville, Maryland
- Device: Johnson & Johnson MedTech RBA-2201 Surgical Robotic Arm
- Reported by: Dr. Emily Chen, Senior Surgeon
- Assigned to: Robotics & Controls Engineering Intern

---

*This project was completed as part of the Johnson & Johnson MedTech Robotics & Controls Engineering virtual internship on the Forage platform.*
