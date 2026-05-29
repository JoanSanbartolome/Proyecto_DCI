# SPI Protocol Implementation

Actionable guidelines for SPI bus design on STM32F746ZG.

## Performance Constraints
- **Max Frequencies:** 54 MHz (SPI1/4/5/6), 27 MHz (SPI2/3). 
- **Critical Limitation:** SPI1 is capped at **40 MHz** if using pin **PA5** for SCK.
- **Flash Compatibility:** SST25VF040B supports **Mode 0 and Mode 3** up to **50 MHz**.

## Signal Integrity (SI)
- **SCK Priority:** The clock signal is the most critical. Route it as directly as possible over a solid ground plane, avoiding vias.
- **Series Termination:** Place a **22-33 $\Omega$** resistor on the SCK line as close to the driving source as possible to eliminate ringing/reflections.
- **Length Matching:** Match lengths of SCK, MOSI, and MISO to a tolerance of **10-15 mm**.
- **CS Trace:** The Chip Select (CS/NSS) is the least critical for high-speed routing. It can be routed around other traces if needed.
- **GPIO Config:** Must use **Very High Speed** (`OSPEEDR = 11`).

## Hardware Design
- **Chip Select:** 10k $\Omega$ pull-up to VDD on CE# pin.
- **Pin-out Caution:** 
    - Do not use **PB13** or **PA7** (reserved for Ethernet RMII).
    - Avoid **PA5** (User LED and frequency penalty).
- **Flash Protection:** Tie WP# and HOLD# to 3.3V if unused.

## Reference Mapping (SPI2)
To avoid routing complexity across opposite sides of the LQFP144 package, use the compact **Port B** assignment. This groups all SPI2 signals on the right side of the MCU.
- **SCK:** PB10 (Pin 69)
- **CS:** PB12 (Pin 73)
- **MISO:** PB14 (Pin 75)
- **MOSI:** PB15 (Pin 76)

[Source: stm32f746zg.pdf, dm00244518.pdf, SST25VF040B-Data-Sheet]
