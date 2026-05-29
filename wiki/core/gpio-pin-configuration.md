# STM32F746ZG GPIO Pin Configuration

> Sources: stm32f746zg.pdf, 2026-05-24
> Raw: [stm32f746zg.pdf](../../raw/stm32f746zg.pdf)

## Overview

This document provides a comprehensive classification of the STM32F746ZG (LQFP144 package) GPIO pins based on their voltage tolerance, alongside detailed alternative functions and specific utilization constraints. 

## Voltage Tolerance Classification

The STM32F746ZG GPIO pins fall into two primary voltage categories. Exceeding the maximum voltage on a pin can cause permanent damage to the microcontroller.

### 1. 5V Tolerant Pins (FT)
These pins can safely receive signals up to 5V when configured as inputs or in open-drain mode with an external pull-up to 5V.

| Port | 5V Tolerant Pins (FT) |
| :--- | :--- |
| **Port A** | PA0, PA1, PA2, PA3, PA6, PA7, PA8, PA9, PA10, PA11, PA12, PA13, PA14, PA15 |
| **Port B** | PB0, PB1, PB2, PB3, PB4, PB5, PB6, PB7, PB8, PB9, PB10, PB11, PB12, PB13, PB14, PB15 |
| **Port C** | PC0, PC1, PC2, PC3, PC4, PC5, PC6, PC7, PC8, PC9, PC10, PC11, PC12, <span style="color:red">PC13</span>, <span style="color:red">PC14</span>, <span style="color:red">PC15</span> |
| **Port D** | PD0, PD1, PD2, PD3, PD4, PD5, PD6, PD7, PD8, PD9, PD10, PD11, PD12, PD13, PD14, PD15 |
| **Port E** | PE0, PE1, PE2, PE3, PE4, PE5, PE6, PE7, PE8, PE9, PE10, PE11, PE12, PE13, PE14, PE15 |
| **Port F** | PF0, PF1, PF2, PF3, PF4, PF5, PF6, PF7, PF8, PF9, PF10, PF11, PF12, PF13, PF14, PF15 |
| **Port G** | PG0, PG1, PG2, PG3, PG4, PG5, PG6, PG7, PG8, PG9, PG10, PG11, PG12, PG13, PG14, PG15 |
| **Port H** | PH0, PH1 (if not used for HSE crystal) |

*Note: Pins marked in <span style="color:red">red</span> have critical usage restrictions (see Non-Recommended Uses section).*

### 2. 3.3V Tolerant Pins (TTa)
These pins are strictly limited to the VDD supply voltage (typically 3.3V) and are directly connected to the ADC/DAC. **Applying 5V will destroy them.**

| Port | 3.3V Tolerant Pins (TTa) |
| :--- | :--- |
| **Port A** | PA4, PA5 |

## Alternative Modes of Operation

Beyond standard General Purpose Input/Output (GPIO) functionality, pins can be multiplexed to hardware peripherals.

| Functional Group | Supported Peripherals & Protocols |
| :--- | :--- |
| **Timers (TIM)** | PWM generation, Input Capture, Output Compare (TIM1 through TIM14). |
| **Communications** | **UART/USART:** Asynchronous serial (up to 8 ports).<br>**I2C:** Synchronous 2-wire bus (up to 4 ports).<br>**SPI:** High-speed synchronous bus (up to 6 ports).<br>**CAN:** Industrial automotive bus (CAN1 and CAN2). |
| **Analog** | **ADC:** Analog-to-Digital conversion (up to 24 channels).<br>**DAC:** Digital-to-Analog voltage generation (PA4, PA5). |
| **Memory & Display** | **FMC:** Flexible Memory Controller for SRAM, SDRAM, NAND, NOR.<br>**QUADSPI:** High-speed external flash interface.<br>**LTDC:** LCD-TFT Display Controller. |
| **Multimedia** | **SAI:** Serial Audio Interface.<br>**DCMI:** Digital Camera Interface.<br>**SPDIFRX:** Digital audio receiver. |
| **Connectivity** | **Ethernet:** MAC with RMII/MII interface.<br>**USB OTG:** Full-Speed (FS) and High-Speed (HS) controllers.<br>**SDMMC:** SD Card and eMMC host interface. |

## <span style="color:red">Non-Recommended Uses & Critical Constraints</span>

Several pins have internal hardware limitations that make them unsuitable for general-purpose high-current or high-speed tasks.

| Pin(s) | Constraint | Reason |
| :--- | :--- | :--- |
| **<span style="color:red">PC13, PC14, PC15</span>** | **Do NOT use to drive LEDs or heavy loads.** Maximum speed: 2 MHz (with 30pF load). | Powered through an internal power switch limited to **3 mA** total sinking capacity. |
| **BOOT0** | Dedicated boot pin. | Must be tied to GND to boot from main Flash. |
| **PA13 (SWDIO), PA14 (SWCLK)** | Avoid using as standard GPIO if debugging is required. | Required for ST-Link programming and in-circuit debugging. |
| **PA4, PA5** | Do not expose to voltages > 3.3V. | Connected directly to analog internal circuitry (TTa). |

## See Also
- [Microcontroller Specifications](Microcontroller.md)
