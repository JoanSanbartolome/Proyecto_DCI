# USB Design Guidelines

> Sources: STM32F746ZG Datasheet; Nucleo-144 User Manual
> Raw: [2026-05-14-usb-impedance-power.md](../../raw/peripherals/2026-05-14-usb-impedance-power.md)

## Overview
Implementation guidelines for USB 2.0 Full Speed connectivity on the STM32F746ZG, focusing on impedance matching and power delivery for custom PCBs.

## Differential Impedance
* **Target:** $90 \Omega \pm 15\%$ differential impedance ($Z_{diff}$).
* **Matching:** The STM32F746ZG integrated transceiver includes internal matching. No external series resistors are required on D+/D- lines.
* **Layout:** Route as a differential pair over a solid GND plane. Avoid vias and ensure length matching between D+ and D-.

## Power Delivery
* **VDDUSB:** Dedicated supply for the USB transceiver. Must be $3.0\text{V}$ to $3.6\text{V}$.
* **VBUS Input:** If using USB to power the board, connect VBUS to the $5\text{V}$ rail through a Schottky diode (e.g., BAT60) to prevent back-powering the host.
* **Sensing:** Connect VBUS to PA9 (VBUS sensing) to allow the MCU to detect connection events.

## Protection
* **ESD:** Use a specialized ESD protection chip like **USBLC6-4SC6** placed close to the USB connector.
