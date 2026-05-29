# SN65HVD230Q-Q1 CAN Transceiver Data

> Source: Texas Instruments SN65HVD230Q-Q1 Datasheet
> Collected: 2026-05-14
> Published: 2024 (Latest Revision)

## Overview
The SN65HVD230Q-Q1 is a 3.3V CAN transceiver designed for automotive applications. It is compatible with 3.3V MCUs like the STM32F746ZG.

## Pinout & Logic
1. **D (TX):** Logic 0 drives dominant state; logic 1 (or floating) drives recessive state.
4. **R (RX):** Outputs logic 0 for dominant; logic 1 for recessive.
8. **Rs (Mode Select):**
   - Tie to GND: High-speed mode (1 Mbps).
   - Resistor to GND (10k-100k): Slope control mode (reduces EMI).
   - Tie to VCC: Standby mode.

## Impedance
- **Input Resistance:** 30 kΩ to 50 kΩ differentially.
- **Bus Impedance:** Standard 120 Ω differential.

## PCB Layout & Termination
- **Differential Impedance:** 120 Ω.
- **Termination:** 120 Ω at ends of the bus.
- **Split Termination:** Two 60 Ω resistors in series with a 4.7 nF capacitor to ground from the center tap for better EMC.
- **Vref (Pin 5):** Outputs VCC/2 (~1.65V), can be used to bias the split termination common-mode point.
