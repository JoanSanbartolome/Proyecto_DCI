# Project Requirements: STM32F746ZG PCB

This document outlines the specific design goals and constraints for the DCI project 2025/2026.

## 🎯 Core Objectives
Design a functional and manufacturable PCB based on the **STM32F746ZG** microcontroller, inspired by the Nucleo-F746ZG reference design but with a custom layout and specific peripheral additions.

## 🔌 Connectivity & Peripherals
| Feature | Implementation Details |
| :--- | :--- |
| **Ethernet** | RJ45 connector (with integrated LEDs) + external magnetic. |
| **USB** | Micro-USB connector. |
| **CAN Bus** | 2x CAN transceivers with optional line termination. Use complex hierarchy. |
| **Analog Inputs** | 2x Signal conditioners for 0-10V range, scaled for MCU ADC. |
| **Storage** | 1x SPI Flash memory (minimum 2Mb). |
| **Expansion** | 1x 16-pin GPIO header (100 mil pitch). |
| **Debug/Prog** | 10-pin SWD connector (100 mil pitch). No integrated ST-LINK. |

## 🛠 Hardware Design Rules
- **Layer Stack-up:** 4 layers preferred. Total thickness maximum **1.6 mm** (Lab Circuits spec).
- **Planes:** Dedicated GND and PWR planes (PWR can be split).
- **Isolation:** Remove copper in all layers beneath the Ethernet magnetic.
- **Mounting:** 4x mounting holes (3.5mm hole, 6.5mm pad) connected to **EARTH**.
- **Layout:** Entirely new component placement and routing.

## 📝 Design Directives
- Use **complex hierarchy** in Altium for CAN and Analog blocks.
- Incorporate design rules via schematic directives.
- Ensure the design is fabricable and functional.

[Source: Propuesta+Diseño+DCI+25-26.pdf]
