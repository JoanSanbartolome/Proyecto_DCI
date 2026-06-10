# Analog Conditioning (0-10V)

> Sources: Design Decisions 2026-05-18

![Analog Conditioning Diagram](../../images/Pasted%20image%2020260526175955.png)
## Overview
Stage for scaling and protecting two 0-10V analog inputs for the STM32F746ZG 3.3V ADC.

## 🔌 Connector: Weidmüller 1845040000
- **Type:** PCB Terminal Block, 4-pole, 3.5mm pitch, 90° entry.
- **Pinout:**
    1. **AIN_1:** 0-10V Signal 1.
    2. **AGND:** Analog Ground Reference.
    3. **AIN_2:** 0-10V Signal 2.
    4. **AGND:** Analog Ground Reference.

## 🛡️ Conditioning Chain (Per Channel)
1. **Protection:** Signal enters via `In_adc` with optional bias network.
2. **Scaling/Bias:**
    - **R18** = 100k$\Omega$, **R19** = 220k$\Omega$.
    - **Purpose:** Configurable high-impedance network.
3. **Buffer (Op-Amp):**
    - **Component:** **OPA350EA/250 (U5)**.
    - **Config:** Voltage Follower (Seguidor).
    - **Supply:** 3.3V (Physical clamp protection).
4. **Anti-Aliasing Filter (Post-Buffer):**
    - **Values:** **R20** = 1k$\Omega$ / **C12** = 100nF.
    - **Cut-off Frequency ($f_c$):** $\approx 1.6 kHz$.
    - **Rationale:** Optimized for industrial sensors (pressure, temperature) while providing robust noise rejection.
5. **ADC Assistance:**
    - **Value:** Filter capacitor acts as a charge reservoir for the ADC's internal $C_{ADC}$.

## 📐 Layout & Hierarchy
- **Altium Hierarchy:** Use a single sub-sheet with `Repeat(CANAL, 1, 2)` to ensure identical routing and component selection.
- **Separation:** Keep analog components away from high-speed digital switching (Ethernet/CAN).
- **Grounding:** Use a dedicated AGND plane/polygon connected to digital GND at a single point (star ground).

### ⚠️ Mixed-Signal Layout Constraint (RMII vs ADC)
- **The Threat:** Pin **PC5** (`RMII_RXD1`, a very fast digital signal) is physically adjacent to **PB0** and **PB1** (Analog inputs `ADC12_IN8`, `ADC12_IN9`). Routing them closely will inject high-frequency digital noise into the analog readings via parasitic capacitance (crosstalk).
- **Mitigation:** 
    1. Separate the trace of PC5 from PB0/PB1 immediately upon exiting the MCU pads.
    2. Maintain at least **3x trace width (3W rule)** separation.
    3. Ensure a solid, unbroken ground plane sits directly beneath these traces.
    4. Never route PC5 parallel to the analog traces.
    5. The RC filter (1k$\Omega$ / 100nF) must be placed as physically close to the MCU's PB0/PB1 pins as possible to shunt injected noise to ground.

## 🔬 ADC Technical Rationale
- **Architecture:** 12-bit SAR (Successive Approximation Register).
- **Internal Impedance ($R_{ADC}$):** $\approx 6k\Omega$.
- **Sampling Strategy:** The Op-Amp buffer reduces the effective input impedance to near zero. Combined with the 10nF assistance capacitor, this ensures the ADC sample-and-hold circuit reaches 12-bit accuracy within the minimum sampling window.

[Source: stm32f746zg.pdf, Design Review 2026-05-24]
