# 🔋 Battery Management System (BMS) — Simulink Model

> End-to-end BMS simulation pipeline: from voltage/current sensing → signal conditioning → microcontroller logic → Kalman Filter SOC estimation

![Status](https://img.shields.io/badge/Status-Completed-3fb950?style=flat-square)
![Tool](https://img.shields.io/badge/Tool-MATLAB%20Simulink-blue?style=flat-square)
![Domain](https://img.shields.io/badge/Domain-Battery%20Systems%20%7C%20Automotive-f0a500?style=flat-square)
![MCU](https://img.shields.io/badge/MCU%20Logic-ESP32%20(Simulated)-purple?style=flat-square)

---

## 📌 Overview

This project implements a fully modular **Battery Management System (BMS)** in **MATLAB Simulink**, simulating a real-world pipeline from raw battery signals to accurate State of Charge (SOC) estimation using a **Kalman Filter**.

The system models the complete signal chain:

```
Battery True Model → LEM Sensor Layer → ESP32 Signal Conditioning → Kalman SOC Estimator → Output / Logging
```

**Why this matters:** Accurate SOC estimation is critical in EVs and energy storage systems. This model demonstrates how sensor noise, scaling errors, and current integration drift are handled in a real BMS pipeline — all verified in simulation before hardware deployment.

---

## 🚀 Project Architecture

### 1️⃣ Battery True Model (`Battery_True_Model`)

Simulates the physical behaviour of a lithium-ion battery cell.

| Output Signal | Description |
|--------------|-------------|
| `V_batt_true` | Actual battery terminal voltage |
| `I_batt` | Load current drawn |
| `SOC_true` | Ground-truth State of Charge (reference) |

- Models internal resistance and open-circuit voltage (OCV) vs SOC relationship
- Driven by a step load profile `I_load_A`

---

### 2️⃣ Sensors Layer — `Sensors_LEM`

Simulates real LEM sensor hardware with realistic scaling and noise behaviour.

| Sensor | Model | Output Signal |
|--------|-------|--------------|
| Voltage Transducer | LEM LV25-P | `V_meas_LV25` |
| Current Sensor | LEM LA-200 | `V_meas_LA200` |
| Temperature Sensor | NTC Thermistor | `V_meas_temp` |

> Outputs include realistic scaling effects mirroring actual hardware behaviour — not ideal measurements.

---

### 3️⃣ ESP32 Logic Block — `ESP_Logic`

Represents the signal conditioning stage normally performed inside a microcontroller firmware.

**Tasks performed:**
- Convert sensor voltages back to engineering values (V, A, °C)
- Scale voltage and current to physical units
- Pass temperature readings downstream
- Prepare clean, conditioned signals for the Kalman estimator

| Output | Description |
|--------|-------------|
| `V_est` | Estimated voltage (conditioned) |
| `I_est` | Estimated current (conditioned) |
| `Temp_est` | Estimated temperature |
| `SOC_est` | Initial SOC estimate from MCU logic |

Logged as time series via **To Workspace** blocks: `V_est_ts`, `I_est_ts`

---

### 4️⃣ Kalman SOC Estimation Block

A **MATLAB Function block** implementing a discrete Kalman Filter for SOC tracking.

**Algorithm steps:**
1. **Prediction:** `SOC_pred = SOC_prev - (I × dt) / Capacity`
2. **Correction:** Residual computed from measured vs predicted voltage
3. **Kalman Gain Update:** Optimally weighs prediction vs measurement noise
4. **Error Minimization:** Final corrected SOC output

| Output | Description |
|--------|-------------|
| `SOC_est` | Final Kalman-filtered SOC value |
| `SOC_true` | Reference ground truth (for comparison) |

---

## 📊 Post-Processing & Plots

After running the simulation, execute in MATLAB Command Window:

```matlab
figure;
plot(SOC_true.Time, SOC_true.Data, 'LineWidth', 1.5); hold on;
plot(SOC_est.Time,  SOC_est.Data,  '--', 'LineWidth', 1.5);
xlabel('Time (s)');
ylabel('SOC (0–1)');
legend('SOC True', 'SOC Estimated');
title('SOC: True vs Estimated (Kalman Filter)');
grid on;
```

This plots the **Kalman-estimated SOC vs ground truth** — the closer the dashed line tracks the solid line, the better the filter performance.

---

## 🔥 Key Features

- ✅ True battery voltage & SOC model (physics-based)
- ✅ Realistic sensor modelling — LEM LV25-P & LA-200
- ✅ ESP32-like signal conditioning block
- ✅ Kalman Filter SOC estimation (prediction + correction loop)
- ✅ Time-series data logging & MATLAB plotting scripts
- ✅ Fully modular Simulink architecture (each block independently testable)

---

## 🗂️ Repository Contents

```
battery-bms-soc-estimation/
│
├── battery_bms_model.zip       # Full Simulink model (extract & open in MATLAB)
├── Screenshot 2025-12-09.png   # Simulink block diagram screenshot
└── README.md
```

**Requirements:** MATLAB R2022a or later · Simulink · Simscape Electrical (optional)

---

## 🔗 How to Run

1. Download and extract `battery_bms_model.zip`
2. Open `battery_bms_model.slx` in MATLAB Simulink
3. Click **Run** (Stop Time: 50s)
4. After simulation, run the MATLAB plotting script above
5. Inspect `SOC_est` vs `SOC_true` in the workspace

---

## 👤 Author

**Elamaran Y** — B.Tech Electronics & Communication Engineering, VIT Vellore
[linkedin.com/in/elamaran-y](https://linkedin.com/in/elamaran-y) · [github.com/Elamaran-hub](https://github.com/Elamaran-hub)
