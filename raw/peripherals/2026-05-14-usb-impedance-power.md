# USB Impedance and Power for STM32F746ZG

> Source: STM32F746ZG Datasheet and Nucleo-F746ZG Manual
> Collected: 2026-05-14
> Published: Unknown

## USB 2.0 Differential Impedance
The USB 2.0 standard specifies a differential impedance (Zdiff) of 90 Ω ± 15%. This is critical to prevent reflections and ensure signal integrity at high speeds.

## STM32F746ZG Implementation
The STM32F746ZG includes an integrated USB transceiver that does not require external series resistors (like the common 22 Ω resistors).
- **Output Impedance (ZDRV):** 28 Ω to 44 Ω (from Datasheet Table 82).
- **Matching:** The matching impedance is included in the embedded driver.

## PCB Layout Recommendations
- **Trace Calculation:** Width and spacing must be calculated for 90 Ω differential impedance based on the PCB stackup and reference plane (GND).
- **Symmetry:** Traces for D+ and D- must be parallel, equal length, and routed over a continuous GND plane.
- **Protection:** Use USBLC6-4SC6 for ESD protection.

## Power Compatibility (CN13)
The User USB connector (CN13) on the Nucleo-144 board cannot power the board directly.
- **Custom Fix:** Connect VBUS to the 5V rail through a Schottky diode or power switch to use USB as a power source.
- **VDDUSB:** Must be between 3.0V and 3.6V for proper transceiver operation.
