🔋 Battery Management System (BMS) — Simulink Model

End-to-end BMS simulation pipeline: from voltage/current sensing → signal conditioning → microcontroller logic → Kalman Filter SOC estimation

Show Image
Show Image
Show Image
Show Image

📌 Overview
This project implements a fully modular Battery Management System (BMS) in MATLAB Simulink, simulating a real-world pipeline from raw battery signals to accurate State of Charge (SOC) estimation using a Kalman Filter.
The system models the complete signal chain:
Battery True Model → LEM Sensor Layer → ESP32 Signal Conditioning → Kalman SOC Estimator → Output / Logging
Why this matters: Accurate SOC estimation is critical in EVs and energy storage systems. This model demonstrates how sensor noise, scaling errors, and current integration drift are handled in a real BMS pipeline — all verified in simulation before hardware deployment.

🚀 Project Architecture
1️⃣ Battery True Model (Battery_True_Model)
Simulates the physical behaviour of a lithium-ion battery cell.
Output SignalDescriptionV_batt_trueActual battery terminal voltageI_battLoad current drawnSOC_trueGround-truth State of Charge (reference)

Models internal resistance and open-circuit voltage (OCV) vs SOC relationship
Driven by a step load profile I_load_A


2️⃣ Sensors Layer — Sensors_LEM
Simulates real LEM sensor hardware with realistic scaling and noise behaviour.
SensorModelOutput SignalVoltage TransducerLEM LV25-PV_meas_LV25Current SensorLEM LA-200V_meas_LA200Temperature SensorNTC ThermistorV_meas_temp

Outputs include realistic scaling effects mirroring actual hardware behaviour — not ideal measurements.


3️⃣ ESP32 Logic Block — ESP_Logic
Represents the signal conditioning stage normally performed inside a microcontroller firmware.
Tasks performed:

Convert sensor voltages back to engineering values (V, A, °C)
Scale voltage and current to physical units
Pass temperature readings downstream
Prepare clean, conditioned signals for the Kalman estimator

OutputDescriptionV_estEstimated voltage (conditioned)I_estEstimated current (conditioned)Temp_estEstimated temperatureSOC_estInitial SOC estimate from MCU logic
Logged as time series via To Workspace blocks: V_est_ts, I_est_ts

4️⃣ Kalman SOC Estimation Block
A MATLAB Function block implementing a discrete Kalman Filter for SOC tracking.
Algorithm steps:

Prediction: SOC_pred = SOC_prev - (I × dt) / Capacity
Correction: Residual computed from measured vs predicted voltage
Kalman Gain Update: Optimally weighs prediction vs measurement noise
Error Minimization: Final corrected SOC output

OutputDescriptionSOC_estFinal Kalman-filtered SOC valueSOC_trueReference ground truth (for comparison)

📊 Post-Processing & Plots
After running the simulation, execute in MATLAB Command Window:
matlabfigure;
plot(SOC_true.Time, SOC_true.Data, 'LineWidth', 1.5); hold on;
plot(SOC_est.Time,  SOC_est.Data,  '--', 'LineWidth', 1.5);
xlabel('Time (s)');
ylabel('SOC (0–1)');
legend('SOC True', 'SOC Estimated');
title('SOC: True vs Estimated (Kalman Filter)');
grid on;
This plots the Kalman-estimated SOC vs ground truth — the closer the dashed line tracks the solid line, the better the filter performance.
