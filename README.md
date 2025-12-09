# battery-bms-soc-estimation
# 🔋 Battery Management System (BMS) – Simulink Model  
### SOC Estimation using Kalman Filter | Sensor Modeling | ESP32 Logic Integration

This project implements a simplified Battery Management System (BMS) in **MATLAB Simulink**, focusing on:

- True Battery Model  
- Sensor Measurement System (LEM LV25-P, LA-200, Temperature Sensor)  
- ESP32 Processing Logic  
- Kalman Filter–based State of Charge (SOC) Estimation  
- Data logging for post-processing and plotting  

The goal is to simulate a real-world BMS pipeline end-to-end, from **voltage/current sensing → signal conditioning → microcontroller logic → SOC estimation**.

---

## 🚀 Project Architecture

### **1️⃣ Battery True Model**
- Generates:
  - `V_batt_true` (Actual battery terminal voltage)
  - `I_batt` (Load current)
  - `SOC_true` (True State of Charge)
- Represents the physical battery behaviour.

### **2️⃣ Sensors Layer (LEM Sensors Block)**
Simulated measurement devices:
- **LV25-P Voltage Transducer (Scaled Output)**
- **LA-200 Current Sensor (Current → Voltage mapping)**
- **Temperature Sensor**
  
Outputs:
- `V_meas_LV25`
- `V_meas_LA200`
- `V_meas_temp`

These outputs include scaling effects similar to real hardware behaviour.

---

## **3️⃣ ESP32 Logic Block**
This block represents the signal conditioning normally done in a microcontroller.

Tasks performed:
- Convert sensor signals to engineering values  
- Scale voltage and current  
- Pass temperature values  
- Prepare cleaned signals for Kalman filter  

Outputs:
- `V_est`
- `I_est`
- `Temp_est`
- `SOC_est` (initial estimate from microcontroller logic)

These are logged using **To Workspace** blocks as time series:
- `V_est_ts`
- `I_est_ts`

---

## **4️⃣ Kalman SOC Estimation Block**
A MATLAB Function block implementing:
- Prediction step (`SOC_pred = SOC_prev - I * dt / Capacity`)
- Correction step using measured voltage  
- Kalman gain update  
- Error minimization  

Outputs:
- `SOC_est` → final Kalman-filtered SOC

Logged as:
- `SOC_est`
- `SOC_true` (reference)

---

## 📊 **Post-Processing & Plots**

After simulation, run in MATLAB:

```matlab
figure;
plot(SOC_true.Time, SOC_true.Data, 'LineWidth',1.5); hold on;
plot(SOC_est.Time, SOC_est.Data, '--', 'LineWidth',1.5);
xlabel('Time (s)');
ylabel('SOC (0–1)');
legend('SOC true','SOC estimated');
title('SOC: True vs Estimated');
grid on;

- `I_est`
- `Temp_est`
- `SOC_est` (initial estimate from microcontroller logic)

These are logged using **To Workspace** blocks as ti
## 🔥 Features
- True battery voltage & SOC model
- Sensor modelling (LV25-P, LA-200)
- ESP32-like signal conditioning
- Kalman Filter SOC estimation
- Timeseries logging & MATLAB plotting
- Fully modular architecture

