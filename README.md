# High-Current H-Bridge DC Motor Driver (DRV8873S)

This repository contains the complete hardware design files for a compact, industrial-grade **H-Bridge DC Motor Driver Module**. Designed in **Altium Designer**, the board is centered around the high-performance **TI DRV8873S** driver IC, capable of driving brushed DC motors with comprehensive diagnostics, current sensing, and robust hardware protection circuits.


<img width="697" height="611" alt="image" src="https://github.com/user-attachments/assets/c4efb819-c967-43e9-afa6-276adbc21526" />


---

## 🚀 Key Hardware Features

* **High-Current H-Bridge Handling:** Powered by the **DRV8873S PWP** package, optimized for low $R_{DS(on)}$ and excellent thermal dissipation.
* **Onboard Reverse Polarity Protection:** Integrated hardware protection stage guarding the entire system against accidental power supply reversal.
* **Real-Time Current Sensing:** Dual-channel proportional current output lines mapped to high-precision shunt networks for dynamic load monitoring.
* **Comprehensive Diagnostic LEDs:** Onboard status LEDs for individual input lines, logic supply, and main motor voltage rails.
* **Robust Connector Separation:** High-current screw terminals for power/motor lines, isolated from fragile logic signaling connectors.

---

## 🔌 Detailed Circuit Architecture & Subsystems

The circuit design is optimized for high-power electrical safety and split into functional blocks as shown in the hierarchical schematics:

<img width="1008" height="502" alt="image" src="https://github.com/user-attachments/assets/e33acc67-5fec-4e36-a364-95c41c7d22cc" />



### 1. Main H-Bridge Drive Core (DRV8873S)
* **Driver IC:** **DRV8873S** manages the full H-bridge switching topology.
* **Charge Pump Circuitry:** Equipped with a **47nF high-voltage ceramic capacitor (C1)** connected across the `CPL` and `CPH` pins to sustain the high-side N-channel MOSFET gate-drive voltages.
* **Decoupling Network:** Multiple decoupling stages (**C2, C3, C5, C6**) ranging from 0.1uF to 1uF are placed directly adjacent to the IC power pins (`VM`, `DVDD`, `VCP`) to suppress high-frequency switching noise.

### 2. Active Reverse-Polarity Barrier Protection
* **The Protection Gate:** To safeguard the driver against reverse power insertion, a dedicated **Barrier Protection** circuit is engineered at the power input stage.
* **Components Integration:** Utilizes a power MOSFET (**Q1**), paired with a Zener diode (**D5**), resistor (**R3**), and capacitor (**S6**). This circuit clamps unauthorized negative voltages instantly, protecting the downstream `VM_protect` power rail without causing the heavy forward voltage drops typical of traditional diodes.

### 3. Precision Current Sensing & Fault Diagnostics
* **Proportional Monitoring:** The `IPROPI1` and `IPROPI2` pins output an analog current proportional to the real-time bridge load current. These are routed across two **680-Ohm resistors (R2, R8)** to convert the current into readable analog voltage levels for external microcontrollers.
* **Hardware Fault Gating:** The open-drain `nFAULT` pin is equipped with a **10k-Ohm pull-up resistor (R1)** to the `VM_protect` rail, providing an active-low hardware interrupt signal whenever an overcurrent, undervoltage, or thermal shutdown occurs.

### 4. Status Signal Interfacing & LEDs Matrix
* **Diagnostic LEDs:** Features 4 independent LED paths (`D1`, `D2`, `D3`, `D4`) driven through tailored current-limiting resistors to give visual tracking for:
  * `IN1` / `IN2`: Active PWM input control lines status.
  * `DVDD`: Logic power rail stability.
  * `VM`: Main motor supply rail status.
* **Control Connectors:** 
  * **J3 Terminal:** Interfaces logic control inputs (`IN1`, `IN2`, `IPROPI1`, `IPROPI2`, `GND`).
  * **J2 Terminal:** Manages system status commands (`nFAULT`, `DISABLE`, `nSLEEP`, `GND`).
