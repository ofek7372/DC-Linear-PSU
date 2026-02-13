# DC Linear Power Supply (dc-linear-psu)

A linear DC power supply featuring a **full‑wave bridge rectifier**, smoothing capacitor, and a **zener‑based linear regulator** designed to deliver approximately **6.1 V** with strict **load regulation (<5%)** and **line regulation (<0.5%)**, validated via simulation and a soldered prototype. [1](https://qmulprod-my.sharepoint.com/personal/ec24673_qmul_ac_uk/Documents/Microsoft%20Copilot%20Chat%20Files/dc_psu_report.pdf)

---

## Table of Contents

- [Overview](#overview)
- [Specifications](#specifications)
- [Architecture](#architecture)
- [Design Decisions](#design-decisions)
  - [Rectifier Selection](#rectifier-selection)
  - [Regulator Evolution](#regulator-evolution)
  - [Ripple & Capacitor Sizing](#ripple--capacitor-sizing)
- [Key Calculations](#key-calculations)
- [Simulation Summary](#simulation-summary)
- [Prototype & Testing](#prototype--testing)
- [Measured Results](#measured-results)
- [Performance Summary](#performance-summary)
- [Limitations & Future Improvements](#limitations--future-improvements)
- [How to Reproduce / Test](#how-to-reproduce--test)
- [Skills Demonstrated](#skills-demonstrated)


---

## Overview

This project explores the end‑to‑end design of a **DC linear PSU** under real‑world constraints (component availability, cost, thermal/power limits), moving from analytical design through **Multisim** simulations to **breadboard** and **stripboard** prototypes. The final build meets the target output of ~**6.1 V** and the specified regulation requirements. [1](https://qmulprod-my.sharepoint.com/personal/ec24673_qmul_ac_uk/Documents/Microsoft%20Copilot%20Chat%20Files/dc_psu_report.pdf)

---

## Specifications

- **Target output voltage:** ~6.1 V  
- **Load regulation:** < 5% (achieved ≈ **4.4%**)  
- **Line regulation:** < 0.5% (achieved ≈ **0.194%**)  
- **AC input:** bench transformer → rectifier (measured ~18 V peak after bridge)  
- **Allowed component ratings:** resistors 0.25 W each  
- **Core components:** 1N4004 bridge, two 6V2 zener diodes in parallel, 16×10 Ω resistors (forming ~40 Ω series network)  
All values and constraints per the project brief and lab availability. [1](https://qmulprod-my.sharepoint.com/personal/ec24673_qmul_ac_uk/Documents/Microsoft%20Copilot%20Chat%20Files/dc_psu_report.pdf)

---

## Architecture
- The supply is documented as a **two‑subsystem** design: **rectifier** and **regulator**, aligned with a 2‑port view used in the report. [1](https://qmulprod-my.sharepoint.com/personal/ec24673_qmul_ac_uk/Documents/Microsoft%20Copilot%20Chat%20Files/dc_psu_report.pdf)

> _Images (optional)_:  
> - `images/rectifier.png` – Bridge + capacitor  
> - `images/regulator.png` – Series resistor network + parallel zeners  
> - `images/prototype_layout.png` – Stripboard layout & soldered build  
> You can export screenshots from your Multisim files and lab photos into `/images`. [1](https://qmulprod-my.sharepoint.com/personal/ec24673_qmul_ac_uk/Documents/Microsoft%20Copilot%20Chat%20Files/dc_psu_report.pdf)

---

## Design Decisions

### Rectifier Selection

- **Why full‑wave (bridge) over half‑wave?**  
  Half‑wave average output: \( V_{\text{avg,HW}} = V_p/\pi \) → for \(V_p = 17\,\text{V}\), \( \approx 5.4\,\text{V} \) (below 6.1 V target).  
  Full‑wave average output: \( V_{\text{avg,FW}} = 2V_p/\pi \) → \( \approx 10.8\,\text{V} \).  
  Full‑wave also **doubles ripple frequency** → easier filtering and better regulation. These considerations made the full‑wave bridge the correct choice despite slightly higher part count. [1](https://qmulprod-my.sharepoint.com/personal/ec24673_qmul_ac_uk/Documents/Microsoft%20Copilot%20Chat%20Files/dc_psu_report.pdf)

### Regulator Evolution

1. **Zener + series resistor (baseline):** simple, but high dissipation in series resistor and **poor load regulation** due to zener current swinging with load. [1](https://qmulprod-my.sharepoint.com/personal/ec24673_qmul_ac_uk/Documents/Microsoft%20Copilot%20Chat%20Files/dc_psu_report.pdf)  
2. **Zener + transistor:** rejected after simulation because the transistor **exceeded power dissipation limits** at max load. [1](https://qmulprod-my.sharepoint.com/personal/ec24673_qmul_ac_uk/Documents/Microsoft%20Copilot%20Chat%20Files/dc_psu_report.pdf)  
3. **Final design — parallel zeners + series resistor network (~40 Ω):**  
   - Run zeners in a **preferred current band ~50–150 mA** to stabilize \(V_Z\).  
   - Choose total current target \( I_{\text{tot}} \approx 295\,\text{mA} \) so that, across load extremes, each zener stays near its optimal range; computed \( I_{Z,\text{max}} \approx 137\,\text{mA} \). [1](https://qmulprod-my.sharepoint.com/personal/ec24673_qmul_ac_uk/Documents/Microsoft%20Copilot%20Chat%20Files/dc_psu_report.pdf)  
   - Implement the ~40 Ω series resistance using **16×10 Ω (0.25 W)** resistors to **share ~3.2 W** and keep each part within rating. [1](https://qmulprod-my.sharepoint.com/personal/ec24673_qmul_ac_uk/Documents/Microsoft%20Copilot%20Chat%20Files/dc_psu_report.pdf)

### Ripple & Capacitor Sizing

- Ripple estimate: \( V_r \approx \dfrac{I_{\text{tot}}}{fC} \) (full‑wave: \(f = 2 f_{\text{mains}}\)).  
- Tested capacitors: **470 µF** (borderline ~0.5% line regulation), **1000 µF** (selected; ~0.2% line regulation), **2200 µF** (works but physically large).  
- Trade‑off: smaller \(C\) saves space but increases ripple → current variation → minor \(V_Z\) movement; **1000 µF** achieved a safe margin under the 0.5% line‑reg spec. [1](https://qmulprod-my.sharepoint.com/personal/ec24673_qmul_ac_uk/Documents/Microsoft%20Copilot%20Chat%20Files/dc_psu_report.pdf)

---

## Key Calculations

- **Series resistance (target \(I_{\text{tot}} \approx 295\,\text{mA}\))**  
  Using \( V_{\text{in}} \approx 18\,\text{V}\) (post‑rectifier peak) and \( V_o \approx 6.2\,\text{V} \):  
  \( R_{\text{eq}} = \dfrac{V_{\text{in}} - V_o}{I_{\text{tot}}} \approx \dfrac{18 - 6.2}{0.295} \approx 40\,\Omega \). [1](https://qmulprod-my.sharepoint.com/personal/ec24673_qmul_ac_uk/Documents/Microsoft%20Copilot%20Chat%20Files/dc_psu_report.pdf)

- **Power in series network (typical):**  
  \( P \approx (V_{\text{in}} - V_o) \cdot I_{\text{tot}} \approx (17 - 6.2)\,\text{V} \cdot 0.295\,\text{A} \approx 3.2\,\text{W} \) →  
  minimum **≈13** resistors at 0.25 W each; **16** used to add margin. [1](https://qmulprod-my.sharepoint.com/personal/ec24673_qmul_ac_uk/Documents/Microsoft%20Copilot%20Chat%20Files/dc_psu_report.pdf)

- **Worst‑case check (peak ripple case):**  
  \( V_{\text{drop}} \approx 12.1\,\text{V} \), \( I \approx 0.3\,\text{A} \Rightarrow P \approx 3.63\,\text{W} \) → requires **≥15** resistors; **16** still safe. [1](https://qmulprod-my.sharepoint.com/personal/ec24673_qmul_ac_uk/Documents/Microsoft%20Copilot%20Chat%20Files/dc_psu_report.pdf)

- **Capacitor sizing (example):**  
  With \( I_{\text{tot}} = 295\,\text{mA}, f = 100\,\text{Hz}, V_r \approx 1\,\text{V} \Rightarrow C \approx 2950\,\mu\text{F} \) → next choices evaluated were **2200 µF**, **1000 µF**, and **470 µF** with measured/regulation trade‑offs noted above. [1](https://qmulprod-my.sharepoint.com/personal/ec24673_qmul_ac_uk/Documents/Microsoft%20Copilot%20Chat%20Files/dc_psu_report.pdf)

---

## Simulation Summary

- **Rectifier output & ripple** matched analytical expectations; observed \( I_{\text{tot}} \approx 250 \text{–} 300\,\text{mA} \) depending on ripple. [1](https://qmulprod-my.sharepoint.com/personal/ec24673_qmul_ac_uk/Documents/Microsoft%20Copilot%20Chat%20Files/dc_psu_report.pdf)  
- **Zener currents** stayed within safe power limits:  
  - For **330 Ω** (light load): each zener ≈ **115–140 mA**; \(V_o \approx 6.2\,\text{V}\). [1](https://qmulprod-my.sharepoint.com/personal/ec24673_qmul_ac_uk/Documents/Microsoft%20Copilot%20Chat%20Files/dc_psu_report.pdf)  
  - For **33 Ω** (heavy load): each zener ≈ **30–50 mA**; \(V_o\) held steady. [1](https://qmulprod-my.sharepoint.com/personal/ec24673_qmul_ac_uk/Documents/Microsoft%20Copilot%20Chat%20Files/dc_psu_report.pdf)  
- **Resistor network dissipation** below limit even at worst case (validated by probe traces). [1](https://qmulprod-my.sharepoint.com/personal/ec24673_qmul_ac_uk/Documents/Microsoft%20Copilot%20Chat%20Files/dc_psu_report.pdf)

---

## Prototype & Testing

- Built first on **breadboard**, then **soldered to stripboard**.  
- Stripboard features: manual **breakpoints** for the bridge, **8‑pin socket** for swapping loads, a **measurement switch** for voltage/current mode, and dedicated **probe wires** for DMM/oscilloscope. [1](https://qmulprod-my.sharepoint.com/personal/ec24673_qmul_ac_uk/Documents/Microsoft%20Copilot%20Chat%20Files/dc_psu_report.pdf)

> **Safety**: Use an isolated bench transformer and standard lab safety practices. Verify there are no shorts before applying power and keep resistor networks clear for adequate cooling.

---

## Measured Results

**Voltage (measured vs expected)**

| Load | Expected V | Measured V | Error |
|------|------------|------------|------:|
| 33 Ω | 6.10 V | 6.17 V | 1.14% |
| 330 Ω | 6.10 V | 6.37 V | 4.42% |

**Current (measured vs expected)**

| Load | Expected I | Measured I | Error |
|------|------------|------------|------:|
| 33 Ω | 186 mA | 192 mA | 3.23% |
| 330 Ω | 19.3 mA | 19.0 mA | 1.58% |

All measured values and images are from the lab testing section. [1](https://qmulprod-my.sharepoint.com/personal/ec24673_qmul_ac_uk/Documents/Microsoft%20Copilot%20Chat%20Files/dc_psu_report.pdf)

---

## Performance Summary

- **Load regulation:**  
  \( \dfrac{V_{\text{NL}} - V_{\text{FL}}}{V_{\text{FL}}} \times 100 \approx \dfrac{6.37 - 6.10}{6.10} \times 100 \approx 4.4\% \) (**< 5% spec**) [1](https://qmulprod-my.sharepoint.com/personal/ec24673_qmul_ac_uk/Documents/Microsoft%20Copilot%20Chat%20Files/dc_psu_report.pdf)

- **Line regulation:**  
  \( \dfrac{\Delta V_o}{\Delta V_i} \times 100 \approx \dfrac{70\,\text{mV}}{36\,\text{V}} \times 100 \approx 0.194\% \) (**< 0.5% spec**) [1](https://qmulprod-my.sharepoint.com/personal/ec24673_qmul_ac_uk/Documents/Microsoft%20Copilot%20Chat%20Files/dc_psu_report.pdf)

- All components operated within **power ratings**, aided by the distributed resistor network. [1](https://qmulprod-my.sharepoint.com/personal/ec24673_qmul_ac_uk/Documents/Microsoft%20Copilot%20Chat%20Files/dc_psu_report.pdf)

---

## Limitations & Future Improvements

- Using many low‑power resistors **increases size** and soldering complexity → replace with **fewer, higher‑power resistors**. [1](https://qmulprod-my.sharepoint.com/personal/ec24673_qmul_ac_uk/Documents/Microsoft%20Copilot%20Chat%20Files/dc_psu_report.pdf)  
- Zener‑based regulation is **temperature/tolerance sensitive** → consider an **integrated linear regulator (e.g., 7806)** for tighter tolerance and thermal behavior. [1](https://qmulprod-my.sharepoint.com/personal/ec24673_qmul_ac_uk/Documents/Microsoft%20Copilot%20Chat%20Files/dc_psu_report.pdf)  
- Add **current limiting / protection** and optionally more **parallel zeners** to improve robustness to ripple/tolerance variations. [1](https://qmulprod-my.sharepoint.com/personal/ec24673_qmul_ac_uk/Documents/Microsoft%20Copilot%20Chat%20Files/dc_psu_report.pdf)

---

## How to Reproduce / Test

1. **Assemble** the bridge rectifier (1N4004 ×4) and connect the smoothing capacitor (**1000 µF** recommended). [1](https://qmulprod-my.sharepoint.com/personal/ec24673_qmul_ac_uk/Documents/Microsoft%20Copilot%20Chat%20Files/dc_psu_report.pdf)  
2. **Build** the series resistor network to ~**40 Ω** using multiple 10 Ω/0.25 W parts (e.g., 16 units) to distribute power. [1](https://qmulprod-my.sharepoint.com/personal/ec24673_qmul_ac_uk/Documents/Microsoft%20Copilot%20Chat%20Files/dc_psu_report.pdf)  
3. **Add** two **6V2 zeners in parallel** across the output. Observe correct polarity. [1](https://qmulprod-my.sharepoint.com/personal/ec24673_qmul_ac_uk/Documents/Microsoft%20Copilot%20Chat%20Files/dc_psu_report.pdf)  
4. **Test** with **33 Ω** and **330 Ω** loads. Measure \(V_o\) and \(I_o\) using DMM; observe ripple and zener currents on the oscilloscope. Compare against the tables above. [1](https://qmulprod-my.sharepoint.com/personal/ec24673_qmul_ac_uk/Documents/Microsoft%20Copilot%20Chat%20Files/dc_psu_report.pdf)  

> Note: Values will vary slightly with component tolerances and temperature; this is expected and discussed in the evaluation. [1](https://qmulprod-my.sharepoint.com/personal/ec24673_qmul_ac_uk/Documents/Microsoft%20Copilot%20Chat%20Files/dc_psu_report.pdf)

---
## Skills Demonstrated

- Analog electronics design under constraints (ripple, regulation, dissipation)  
- Circuit simulation & probe‑based verification (Multisim)  
- Power budgeting & resistor network design for thermal limits  
- Prototyping on breadboard and stripboard; test planning & measurement  
- Technical documentation and result evaluation  

These align with the analysis, simulations, prototype work, testing plan, and final evaluation from the project. [1](https://qmulprod-my.sharepoint.com/personal/ec24673_qmul_ac_uk/Documents/Microsoft%20Copilot%20Chat%20Files/dc_psu_report.pdf)

---


