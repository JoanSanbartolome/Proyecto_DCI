# SPI Protocol Synthesis: STM32F746ZG & SST25VF040B

This document synthesizes recommendations and technical specifications for implementing the SPI protocol in the custom PCB project.

## 1. Technical Specifications

### STM32F746ZG (Master)
- **Clock Frequencies:**
  - **SPI1, 4, 5, 6:** Up to **54 MHz** (Master mode, 2.7V $\le$ VDD $\le$ 3.6V).
  - **SPI2, 3:** Up to **27 MHz**.
  - **Constraint:** SPI1 max frequency is reduced to **40 MHz** if SCK is mapped to **PA5**.
- **I/O Configuration:**
  - **Speed:** Set GPIO `OSPEEDR` to `11` (Very High Speed).
  - **Levels:** 3.3V CMOS.

### SST25VF040B (Slave Flash)
- **Supported Modes:** Mode 0 (CPOL=0, CPHA=0) and Mode 3 (CPOL=1, CPHA=1).
- **Maximum Frequency:** 50 MHz.
- **Pin Logic:** Data sampled on rising edge of SCK, driven on falling edge.

## 2. Signal Integrity (SI) & Layout Recommendations

### Impedance Control
- **General Rule:** No strict impedance requirement for "electrically short" traces (length < 10% of signal rise time distance).
- **Target:** Aim for **50 $\Omega$** if using controlled impedance (simplifies fabrication if other high-speed buses are present).
- **Termination:** Place **22 $\Omega$ to 33 $\Omega$** series resistors close to the driver (MCU for SCK/MOSI, Flash for MISO) to damp ringing and slow down edges.

### Physical Layout
- **Ground Plane:** Route traces over a continuous solid ground plane to provide a clear return path and reduce EMI.
- **Trace Matching:** Keep SCK, MISO, and MOSI traces matched in length (within $\pm$ 10mm) for high-speed operation (>20 MHz).
- **Separation:** Maintain distance from high-speed switching lines (e.g., Ethernet) to avoid crosstalk.

## 3. Hardware Implementation

### Multi-Slave Strategy
- **Topology:** Star topology is preferred for connecting expansion headers and on-board Flash.
- **NSS Management:** Use **Software NSS** (Standard GPIOs) for Chip Select to support multiple slaves easily.
- **Pull-ups:** Add a **10k $\Omega$ pull-up** on the CS (CE#) line to ensure the Flash remains disabled during reset.

### Pin Selection Conflicts (Nucleo-144)
- **Ethernet:** Avoid PB13 (RMII_TXD1) and PA7 (RMII_DV) if Ethernet is required.
- **LEDs:** Avoid PA5 if the onboard User LED is needed (also limited to 40MHz for SPI1).
- **Recommended SPI2 Pins:** SCK: PB10, MISO: PC2, MOSI: PC3, CS: PB12.

[Sources: stm32f746zg.pdf, dm00244518.pdf, SST25VF040B-Data-Sheet, Altium SPI SI Article]
