# Task 2 — Design Optimization for Enhanced Surgical Arm Responsiveness

## Overview

Following the software fix in Task 1, this phase focuses on **hardware-level design modifications** to sustainably improve the RBA-2201's performance, durability, and real-time responsiveness beyond what software alone can achieve.

---

## Three Design Modifications Proposed

### Modification A — Joint Assembly Material Upgrade

**Problem:** Steel bearing surfaces produce a friction coefficient of 0.42, increasing command latency and causing progressive mechanical wear.

**Solution:**
- Replace steel sleeve with **Ti-6Al-4V titanium alloy** (40% mass reduction, 950 MPa tensile strength)
- Apply **medical-grade PTFE coating** to bearing surfaces → friction: 0.42 → **0.27**
- Integrate sealed self-lubricating reservoir for maintenance-interval stability

**Expected gain:** 31% friction reduction, ~0.11 s latency improvement

---

### Modification B — Dual-Actuator Split-Drive Architecture

**Problem:** Single-actuator design causes 38% load variance across command cycles, leading to torque irregularity and motor fatigue risk.

**Solution:**
- Replace single motor with **two matched brushless DC motors** sharing the rotational load
- Implement **differential torque controller** for real-time dynamic load balancing
- Maintain existing mounting form factor for backward compatibility

**Expected gain:** Load variance 38% → **17%**, improved torque linearity

---

### Modification C — Sensor Relocation and Dynamic Recalibration

**Problem:** Encoders positioned 28 mm from the joint pivot introduce a 48 ms feedback delay, causing the closed-loop controller to act on stale position data.

**Solution:**
- Relocate encoders to **6 mm from joint pivot** (79% shorter signal path)
- Upgrade to **16-bit absolute encoder** (resolution: 0.0055°/count vs 0.088°/count)
- Implement **100 Hz dynamic recalibration** in a dedicated real-time OS thread

**Expected gain:** Feedback delay 48 ms → **31 ms** (-35%)

---

## Simulation Results

| Metric | Baseline | Mod A | Mod B | A+B+C |
|--------|----------|-------|-------|-------|
| rotate_joint latency (s) | 0.35 | 0.24 | 0.21 | **0.16** |
| Joint friction coefficient | 0.42 | 0.27 | 0.42 | **0.27** |
| Actuator load variance (%) | 38% | 38% | 19% | **17%** |
| Sensor feedback delay (ms) | 48 | 48 | 48 | **31** |
| System reliability score | 71/100 | 82/100 | 85/100 | **94/100** |

Simulations run using **NumPy** and **SciPy** across 50 randomised command cycles. Combined A+B+C configuration produced a standard deviation of 0.008 s across all latency measurements.

---

## Tools Used

- Python 3 (NumPy, SciPy, Matplotlib)
- Control System Diagnostic Notebook
- Root cause analysis + iterative simulation

---

📄 Full proposal: [`reports/Design_Proposal_RBA2201_Task2.docx`](../reports/Design_Proposal_RBA2201_Task2.docx)
