# DC Linear Power Supply (dc-linear-psu)
--
A linear DC power supply featuring a full‑wave bridge rectifier, smoothing capacitor, and a zener‑based linear regulator.
The design delivers approximately 6.1 V with strict load regulation (<5%) and line regulation (<0.5%), validated through simulation and a soldered prototype.


## Table of Contents

- [Overview]
- [Specifications]
- [Architecture]
- [Design Decisions]
  - [Rectifier Selection]
  - [Regulator Evolution]
  - [Ripple & Capacitor Sizing]
- [Simulation Summary]
- [Prototype & Testing]
- [Measured Results]
- [Performance Summary]
- [Limitations & Future Improvements]
- [How to Reproduce / Test]
- [Skills Demonstrated]
---

## Overview

This project explores the end‑to‑end design of a **DC linear PSU** under real‑world constraints (component availability, cost, thermal/power limits), moving from analytical design through **Multisim** simulations to **breadboard** and **stripboard** prototypes. 
The final build meets the target output of ~**6.1 V** and the specified regulation requirements.


---

## Specifications

- **Target output voltage:** ~6.1 V  
- **Load regulation:** < 5% (achieved ≈ **4.4%**)  
- **Line regulation:** < 0.5% (achieved ≈ **0.194%**)  
- **AC input:** bench transformer → rectifier (measured ~18 V peak after bridge)  
- **Allowed component ratings:** resistors 0.25 W each  
- **Core components:** 1N4004 bridge, two 6V2 zener diodes in parallel, 16×10 Ω resistors (forming ~40 Ω series network)  
All values and constraints per the project brief and lab availability. 

---

## Architecture
- The supply is documented as a **two‑subsystem** design:- **rectifier** and **regulator**, aligned with a 2‑port view used in the report.
-  Rectifier subsystem: full‑wave bridge rectifier and smoothing capacitor
-  Regulator subsystem: resistor network and parallel zener diodes
---

## Design Decisions

### Rectifier Selection
A full‑wave bridge rectifier was chosen instead of a half‑wave design because:
- Half‑wave rectification produces too low an average DC value (≈5.4 V from 17 V peak), below the required 6.1 V.
- Full‑wave output is ≈10.8 V average — sufficient headroom for smoothing and regulation.
- Ripple frequency doubles (100 Hz instead of 50 Hz), making ripple easier to filter and improving load/line regulation.
- Current stress is distributed across diode pairs, improving reliability.


## Regulator section
Three regulator topologies were tested:
1. **Zener + series resistor:** simple, but high dissipation in series resistor and **poor load regulation** due to zener current swinging with load.  
2. **Zener + transistor:** provide improved regulation in theory but rejected after simulation because the transistor **exceeded power dissipation limits** at max load.
3. **Final design — parallel zeners + series resistor network (~40 Ω):**  
   - Run zeners in a **preferred current band ~50–150 mA** to stabilize the zener voltage. 
   -Total current was set to around 295 mA to maintain stable regulation across load conditions.
   -16 resistors (10 Ω each) were used to safely share total power dissipation.

final desin approach offered strong regulation, safe power dissipation, and robustness to component tolerance variations.
  
## Ripple and capacitor Sizing:
-
-Ripple affects the input to the regulator and therefore impacts line regulation.
-Capacitors tested: 470 µF, 1000 µF, and 2200 µF.
-1000 µF provided a good balance between ripple reduction, regulation margin, and physical size.
-470 µF was borderline, producing line regulation close to the upper limit.
-2200 µF was effective but unnecessarily large for the constraints.
---

## Simulation Summary

- **Rectifier output & ripple** matched analytical expectations  
- **Zener currents** stayed within safe power limits 
  - For **330 Ω** (light load): each zener ≈ **115–140 mA** aprox 6.2[v]
  - For **33 Ω** (heavy load): each zener ≈ **30–50 mA**
  - output voltage held steady around 6.2[v]. 
- **Resistor network dissipation** below limit even at worst case (validated by probe traces). 

---

## Prototype & Testing

- Built first on **breadboard**, then **soldered to stripboard**.  
- Stripboard features: manual **breakpoints** for the bridge, **8‑pin socket** for swapping loads, a **measurement switch** for voltage/current mode, and dedicated **probe wires** for DMM/oscilloscope.

> **Safety**: Use an isolated bench transformer and standard lab safety practices. Verify there are no shorts before applying power and keep resistor networks clear for adequate cooling.

---

## Measured Results

**Voltage (measured vs expected)**

| Load | Expected V | Measured V | Error |
|------|------------|------------|------:|
| 33 Ω | 6.10 V     | 6.17 V     | 1.14% |
| 330 Ω | 6.10 V    | 6.37 V     | 4.42% |

**Current (measured vs expected)**

| Load | Expected I | Measured I | Error |
|------|------------|------------|------:|
| 33 Ω | 186 mA     | 192 mA     | 3.23% |
| 330 Ω | 19.3 mA   | 19.0 mA    | 1.58% |

All measured values and images are from the lab testing section.


---

## Limitations & Future Improvements

- Using many low‑power resistors **increases size** and soldering complexity → replace with **fewer, higher‑power resistors**.   
- Zener‑based regulation is **temperature/tolerance sensitive** → consider an **integrated linear regulator (e.g., 7806)** for tighter tolerance and thermal behavior.   
- Add **current limiting / protection** and optionally more **parallel zeners** to improve robustness to ripple/tolerance variations. 

---

## How to Reproduce / Test

1. **Assemble** the bridge rectifier (1N4004 ×4) and connect the smoothing capacitor (**1000 µF** recommended).   
2. **Build** the series resistor network to ~**40 Ω** using multiple 10 Ω/0.25 W parts (e.g., 16 units) to distribute power.  
3. **Add** two **6V2 zeners in parallel** across the output. Observe correct polarity.   
4. **Test** with **33 Ω** and **330 Ω** loads. Measure output and input voltage using DMM; observe rip ple and zener currents on the oscilloscope. Compare against the tables above.  

> Note: Values will vary slightly with component tolerances and temperature; this is expected and discussed in the evaluation.

---
## Skills Demonstrated

- Analog electronics design under constraints (ripple, regulation, dissipation)  
- Circuit simulation & probe‑based verification (Multisim)  
- Power budgeting & resistor network design for thermal limits  
- Prototyping on breadboard and stripboard; test planning & measurement  
- Technical documentation and result evaluation  

These align with the analysis, simulations, prototype work, testing plan, and final evaluation from the project.


