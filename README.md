# Analog Telemetry and Signal Conditioning System

## System Overview
This repository details the design, simulation, and fabrication of a complete two-channel analog telemetry system. The architecture is engineered to acquire remote temperature data, convert it into a noise-immune frequency signal for transmission across long cable runs, and reconstruct the analog data at a receiving base station. Finally, the system performs hardware-level analog arithmetic for real-time sensor fusion (averaging) and gradient measurement (differential).


---

## 1. Transmitter: Acquisition and Modulation
The acquisition board serves as the remote sensing node, designed to interface with analog transducers and condition the signal for harsh or electromagnetically noisy environments.

* **Transducer Interface:** Designed for the AD590 temperature sensor, which behaves as a high-impedance temperature-dependent current source ($Io=K\cdot T$, where $K = 1\mu A/K$). 
* **Current-to-Voltage (I/V) Stage:** A UA741 operational amplifier in an inverting topology converts the micro-ampere current into a scaled voltage. A precision Zener diode network and multi-turn trimmers are implemented to null the thermal offset, ensuring **0°C** yields precisely **0V**.
* **Voltage-to-Frequency (V/F) Modulator:** An XR4151 integrated circuit converts the scaled analog voltage into a train of square wave pulses. The output frequency is directly proportional to the measured temperature, protecting the data integrity from voltage drops and electromagnetic interference (EMI) during transmission.

## 2. Receiver: Demodulation and Analog Processing
The reception board acts as the base station, reconstructing the transmitted data and performing continuous analog computations.

* **Frequency-to-Voltage (F/V) Demodulator:** An XR4151 IC is configured to perform the inverse operation of the transmitter, integrating the incoming pulse train back into a continuous DC voltage calibrated to a specific **V/Hz** ratio.
* **Active Filtering:** The reconstructed signal is passed through a passive RC low-pass filter (**fc ≈ 15Hz**) to eliminate residual high-frequency switching ripple, followed by an LF351 op-amp voltage follower (buffer) for impedance matching and stage isolation.
* **Hardware-Level Arithmetic:** The board processes the two independent temperature channels in real-time without the need for a microcontroller:
  * **Gradient Measurement (Differential):** Amplifies the difference between the two sensors to monitor temperature drift or thermal gradients. The transfer function is $Vo=5(V_2-V_1)$.
  * **Redundancy & Averaging (Summing):** Averages the two incoming signals to provide a stabilized mean temperature, governed by $Vo=0.5(V_1+V_2)$.

---

## Applications
While microcontrollers and digital ADCs are ubiquitous today, analog telemetry remains critical in specific industrial applications. 
* **Noise Immunity:** By transmitting frequency rather than voltage, this system replicates the robust nature of industrial 4-20mA loops. It is highly resistant to ground loops, line resistance, and EMI, making it ideal for HVAC monitoring, chemical plant sensors, or remote weather stations.
* **Zero-Latency Processing:** Performing summing and differential amplification at the hardware level ensures continuous, zero-latency processing. This is highly beneficial in closed-loop control systems where ADC sampling delays or MCU overhead could introduce phase lag.
* **Calibration & Precision:** The inclusion of variable trimmers across both the acquisition and reception stages allows for strict calibration of zero-offsets and amplifier gains, compensating for component tolerances in real-world deployments.
