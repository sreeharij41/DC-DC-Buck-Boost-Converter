 Non-Isolated DC-DC Buck-Boost Converter
 
This repository details the complete process from theoretical design to physical bench-testing—of a **Non-Isolated Inverting DC-DC Buck-Boost Converter**. This project was executed for the EE 252: Electrical Machines and Power Electronics Laboratory at the Indian Institute of Technology (IIT) Indore.

## 🎯 Project Objective
The primary goal is the successful hardware implementation of an inverting Buck-Boost converter capable of regulating output voltage across a wide range, driven by a custom-built PWM controller board. A key academic requirement is the physical demonstration of both Continuous Conduction Mode (CCM) and Discontinuous Conduction Mode (DCM).

## ⚙️ Hardware Specifications & Component Selection

| Parameter / Component | Value / Specific Part | Notes |
| :--- | :--- | :--- |
| **Input Voltage ($V_{in}$)** | 15 V DC | Fixed bench power supply |
| **Target Output Range ($V_{out}$)** | 8 V to 18 V | Tuned via duty cycle ($D$) |
| **Nominal Switching Freq ($f_s$)** | 12.5 kHz | User-adjustable via potentiometer |
| **Max Output Current ($I_{out}$)** | 1 A | Modulated by load rheostat |
| **Power Inductor ($L$)** | 1 mH Toroidal | Essential for energy storage |
| **Output Filter Capacitor ($C_{out}$)** | 470 µF | Smooths voltage ripple |
| **PWM Controller IC** | TL494 | Generates core timing signals |
| **MOSFET Gate Driver IC** | TC4428A | Provides high peak-current switching |
| **Power Switch** | IRFZ44NPbF MOSFET | Mounted with heat sink for thermal stability |
| **Freewheeling Diode** | QH08TZ600 | Hyperfast diode to minimize switching losses |

## 🧠 Operational Theory & Mathematical Foundation

A standard non-isolated Buck-Boost converter naturally inverts the output voltage relative to the input. The ideal voltage conversion ratio in Continuous Conduction Mode (CCM) is governed by the formula:

$$\frac{V_o}{V_{in}} = -\frac{D}{1-D}$$
**The Two-State Operation:**
1.  **State 1 (MOSFET ON):** The switch is closed. The input source directly charges the 1 mH inductor, building a magnetic field. The diode is reverse-biased, isolating the load. During this time, the output capacitor supplies the required load current.
2.  **State 2 (MOSFET OFF):** The switch opens. The inductor's magnetic field collapses, forcing current through the  forward-biased diode and into the load/capacitor network. 

## 🔌 Gate Driver Architecture
Directly driving a heavy power MOSFET like the IRFZ44N from a logic-level signal is inefficient and dangerous due to the MOSFET's internal gate capacitance ($C_{iss}$). 

To solve this, our hardware utilizes a two-stage approach:
1.  **The TL494 Controller:** It utilizes an internal oscillator (tuned via the `F-VARY` potentiometer) and an error amplifier (tuned via the `D-VARY` potentiometer) to output a stable, adjustable PWM signal.
2.  **The TC4428A Driver:** It takes the weak logic signal from the TL494 and amplifies the current, aggressively charging and discharging the MOSFET gate to ensure rapid transitions between ON and OFF states, thereby drastically reducing thermal switching losses.

## 📊 Experimental Results & Oscilloscope Validation

The prototype was successfully validated on the bench, demonstrating clear agreement with theoretical calculations for both step-up (Boost) and step-down (Buck) regulation.

### 1. Continuous Conduction Mode (CCM)
* Achieved under standard load conditions.
* Oscilloscope captures confirm the inductor current never reaches zero amps. We successfully isolated and recorded the Switch Current and Diode Voltage waveforms to prove the handoff of current between the active and passive switches.

### 2. Discontinuous Conduction Mode (DCM)
* As per group specifications, DCM was induced by **increasing the load resistance** using the sliding rheostat, which starves the circuit of average current.
* Oscilloscope captures in the report clearly show the current waveform flatlining at zero, proving the 1 mH inductor fully dumps its stored magnetic energy before the subsequent switching cycle begins.

## 💻 Simulation
Prior to hardware assembly, the circuit topology was modeled in MATLAB/Simulink. The simulation phase confirmed the mathematical conversion ratios and provided a expectation for the inductor current ripple and voltage stress across the switching components. Discrepancies between the simulated and real-world results were minimal and easily attributed to real-world parasitics (Capacitor ESR, Inductor winding resistance, and Diode forward voltage drop).
