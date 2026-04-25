# SPI Protocol Implementation

Actionable guidelines for SPI bus design on STM32F746ZG.

## Performance Constraints
- **Max Frequencies:** 54 MHz (SPI1/4/5/6), 27 MHz (SPI2/3). 
- **Critical Limitation:** SPI1 is capped at **40 MHz** if using pin **PA5** for SCK.
- **Flash Compatibility:** SST25VF040B supports **Mode 0 and Mode 3** up to **50 MHz**.

## Signal Integrity (SI)
- **Series Termination:** **22-33 $\Omega$** resistors must be placed close to the driver to prevent ringing.
- **Impedance:** **50 $\Omega$** target (optional for short runs, recommended for consistency).
- **Length Matching:** Match SCK/MOSI/MISO within **10 mm**.
- **GPIO Config:** Must use **Very High Speed** (`OSPEEDR = 11`).

## Hardware Design
- **Chip Select:** 10k $\Omega$ pull-up to VDD on CE# pin.
- **Pin-out Caution:** 
    - Do not use **PB13** or **PA7** (reserved for Ethernet RMII).
    - Avoid **PA5** (User LED and frequency penalty).
- **Flash Protection:** Tie WP# and HOLD# to 3.3V if unused.

## Reference Mapping (SPI2)
- **SCK:** PB10
- **MISO:** PC2
- **MOSI:** PC3
- **CS:** PB12 (Software NSS recommended)

[Source: stm32f746zg.pdf, dm00244518.pdf, SST25VF040B-Data-Sheet]
