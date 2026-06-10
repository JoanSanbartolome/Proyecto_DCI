# Design Review (2026-05-18)

> Sources: [STM32_Esqumaticos.pdf](../../raw/prints/STM32_Esqumaticos.pdf)
> Status: **REMEDIAL ACTION REQUIRED**

## 🎯 Review Objective
Validation of the current schematic state against project requirements and engineering best practices for the STM32F746ZG custom PCB.

## ✅ Verified Correct
- **Ethernet ESD:** USBLC6-4SC6 correctly implemented for differential pairs.
- **CAN Transceiver:** SN65HVD230Q (3.3V) successfully integrated as per wiki requirements.
- **GPIO Protection:** ESDA6V1BC6 arrays applied to expansion headers.

## ❌ Critical Errors (Action Required)

### 1. Analog Input Scaling (ADC Section)
- **Finding:** R18=10K and R19=22K divider for 0-10V inputs.
- **Result:** Nodes output ~6.87V, exceeding the 3.3V supply of the Op-Amp and MCU.
- **Correction:** Change **R19 to 4.3 k$\Omega$** to achieve ~3.0V scaling.

### 2. Op-Amp Stability (U5B)
- **Finding:** Second channel of OPA350 (U5B) has pins marked NC (No Connect).
- **Result:** Floating inputs will cause oscillation, noise, and excessive power consumption.
- **Correction:** Configure as a buffer: Connect Pin 5 (In+) to AGND, and short Pin 6 (In-) to Pin 7 (OUT).

### 3. USB Signal Integrity (D45)
- **Finding:** ESDA6V1BC6 used for USB D+/D- lines.
- **Result:** ~20pF capacitance will distort high-speed USB data edges, causing communication failures.
- **Correction:** Replace with **USBLC6-4SC6** or **USBLC6-2SC6** (~3.5pF).

### 4. CAN Common Mode Filter (C18)
- **Finding:** C18 = 4.7 pF.
- **Result:** Value is too low to provide any meaningful common-mode noise suppression.
- **Correction:** Change to **4.7 nF**.

## 📐 Layout Directives for MB1137.PcbDoc (New Design)
- **Ethernet:** Maintain 100 $\Omega$ differential impedance. Symmetrical "opening" of traces at USBLC6-4SC6 pads.
- **CAN:** Flow-through routing over TVS pads.
- **Analog:** AGND plane isolation. Interleave GND between signal pins on the Weidmüller connector.
