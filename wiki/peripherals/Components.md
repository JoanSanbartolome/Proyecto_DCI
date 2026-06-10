# Component Selection: Selected Parts Database

> Sources: Joan San Bartolome, 2026-06-10; STMicroelectronics, 2016-02-15; Microchip, 2015-08-01; Texas Instruments, 2003-09-12
> Raw: [STM32_Esqumaticos.pdf](../../raw/prints/STM32_Esqumaticos.pdf); [sn65hvd230q-q1.pdf](../../raw/datasheets/sn65hvd230q-q1.pdf); [SST25VF040B-4-Mbit-SPI-Serial-Flash-Data-Sheet-20005051F.pdf](../../raw/datasheets/SST25VF040B-4-Mbit-SPI-Serial-Flash-Data-Sheet-20005051F.pdf); [1845040000_en.pdf](../../raw/datasheets/1845040000_en.pdf); [esda6v1bc6.pdf](../../raw/datasheets/esda6v1bc6.pdf)

## Overview
This document lists the selected integrated circuits, passive networks, protection devices, and connectors chosen for the custom STM32F746ZG PCB, extracted from the schematic prints and layout constraints.

## 🎛️ Microcontroller & Clocking

### 1. STM32F746ZGT6 (MCU)
*   **Manufacturer:** STMicroelectronics
*   **Package:** LQFP-144 (20x20 mm, 0.5 mm pitch)
*   **Architecture:** ARM Cortex-M7 with FPU, running up to 216 MHz.
*   **Pin Mapping Role:** Central controller managing RMII MAC, bxCAN1/2, SPI2, ADC, and USB OTG FS.
*   **Design Constraints:** Requires 13x 100nF decoupling capacitors, 1x 4.7µF bulk capacitor, and 2x 2.2µF low-ESR ceramic capacitors on VCAP pins (VCAP_1 Pin 71, VCAP_2 Pin 106) for core voltage regulation.

### 2. Seiko Epson NX3215SA (32.768 kHz Crystal)
*   **Application:** LSE (Low-Speed External) Clock for Real-Time Clock (RTC).
*   **Frequency:** 32.768 kHz
*   **Load Capacitance:** 4.3 pF (requires no external capacitors as indicated by `[N/A]` on C41 and C42 in schematics).

### 3. NDK NX3225GD-8.000M (8.000 MHz Crystal)
*   **Application:** HSE (High-Speed External) Clock for main PLL configuration.
*   **Frequency:** 8.000 MHz
*   **Capacitance:** 4.3 pF load (C45, C47).

### 4. NDK X53T-C20SSA-25.000MHz (25.000 MHz Crystal)
*   **Application:** Reference oscillator for LAN8742A Ethernet PHY.
*   **Frequency:** 25.000 MHz
*   **Load Capacitance:** 30 pF (C6, C11).

---

## 💾 Storage & Peripherals

### 5. SST25VF040B-50-4I-S2AF (SPI NOR Flash)
*   **Manufacturer:** Microchip / Silicon Storage Technology (SST)
*   **Package:** SOIC-8 (150 mil width)
*   **Density:** 4 Mbit (exceeds 2 Mbit requirement).
*   **Bus Speed:** Up to 50 MHz.
*   **Interface:** SPI (connected to SPI2: CE# on PB12, SO on PB14, SI on PB15, SCK on PB10).
*   **Methods Applied:** Series damping resistor ($33\,\Omega$ on SCK line, R32) to prevent reflection; $10\,\text{k}\Omega$ pull-up resistors on WP#, HOLD#, and CE# (R31) lines.

### 6. LAN8742A-CZ-TR (Ethernet PHY)
*   **Manufacturer:** Microchip / SMSC
*   **Package:** QFN-24 (4x4 mm, exposed pad grounded via Pin 25).
*   **Function:** 10/100 Ethernet Transceiver.
*   **Interface:** RMII (Reduced Media Independent Interface) connected to MCU.
*   **Requirements:** Solder the exposed center thermal pad directly to the GND plane. Use an external $12.1\,\text{k}\Omega$ 1% bias resistor (R15) on RBIAS (Pin 24).

### 7. SN65HVD230QDRG4Q1 (CAN Transceiver - x2)
*   **Manufacturer:** Texas Instruments
*   **Package:** SOIC-8
*   **Supply Voltage:** 3.3V (avoids 5V levels on logic lines).
*   **Role:** Dual-node transceiver interfacing CAN1 (TX on PD1, RX on PD0) and CAN2 (TX on PB6, RX on PB5).
*   **Aesthetics/Performance:** Slope control configurable via pin 8 (RS) with grounding resistor to adjust EMI emission profile.

### 8. OPA350EA/250 (Analog Buffer Op-Amp - x2)
*   **Manufacturer:** Texas Instruments
*   **Package:** Micro6 (SOT-23-5)
*   **Function:** High-speed Rail-to-Rail input/output operational amplifier configured as a voltage follower buffer for Analog Inputs.
*   **Supply:** 3.3V analog rail (AVDD).

### 9. TSV631AILT (General Purpose Op-Amp)
*   **Manufacturer:** STMicroelectronics
*   **Package:** SOT-23-5
*   **Function:** 3.3V operational amplifier for auxiliary analog functions (e.g. driving indicators or conditioning auxiliary inputs).

---

## ⚡ Power Management & Protection

### 10. LD39050PU33R (LDO Regulator)
*   **Manufacturer:** STMicroelectronics
*   **Package:** DFN-6 (3x3 mm)
*   **Function:** Low-Dropout regulator scaling 5V USB power to 3.3V system power.
*   **Max Current:** 500 mA.
*   **Integrity:** Features Power Good pin (PG) used to drive indicators or control resets, and Enable pin (EN).

### 11. USBLC6-4SC6 (ESD Protection Array - x2)
*   **Manufacturer:** STMicroelectronics
*   **Package:** SOT-23-6L
*   **Capacitance:** Ultra-low ($3.5\,\text{pF}$) to maintain signal integrity on high-speed lines.
*   **Application:**
    *   **USB Port:** D45 on USB_DM/USB_DP lines.
    *   **Ethernet MDI:** U11 protecting TX+/TX- and RX+/RX- differential pairs.

### 12. PESD2CANFD24V-TR (CAN Bus ESD protection - x2)
*   **Manufacturer:** Nexperia
*   **Package:** SOT-23
*   **Function:** Dual-line ESD protection designed specifically for CAN FD and CAN high-speed buses.
*   **Placement:** D42 (CAN2) and D43 (CAN1) placed adjacent to bus terminal connectors.

### 13. ESDA6V1BC6 (TVS Diode Array - x4)
*   **Manufacturer:** STMicroelectronics
*   **Package:** SOT-23-6
*   **Function:** 4-channel bidirectional ESD protection array for GPIO extension headers.
*   **Application:** D46, D47, D48, and D49 located on Sheet 4 to protect external GPIO interfaces from electrostatic discharge.

### 14. Bourns MF-MSMF050-2 (PPTC Resettable Fuse)
*   **Role:** Overcurrent protection on VBUS USB input line.
*   **Rating:** 500 mA hold current.

### 15. Murata BLM18PG121SN1D (Ferrite Bead)
*   **Manufacturer:** Murata
*   **Package:** 0603 (1608 Metric)
*   **Electrical Specs:** $Z = 120\,\Omega \pm 25\%$ @ $100\,\text{MHz}$, $\text{DCR} \le 50\,\text{m}\Omega$, $I_{rated} = 2.0\,\text{A}$.
*   **Application:** L41 on the $V_{DDA}$ / $V_{REF+}$ analog supply line to isolate high-frequency digital noise from the sensitive analog domain.
*   **Design Rationale:**
    *   **Ultra-low DCR:** At $2-5\,\text{mA}$ ADC consumption, the voltage drop is $< 0.15\,\text{mV}$ ($< 0.18\,\text{LSBs}$ at 12-bit), preserving DC measurement accuracy.
    *   **Current Saturation Margin:** The $2.0\,\text{A}$ current rating prevents magnetic saturation under normal operating current ($< 5\,\text{mA}$), maintaining full $120\,\Omega$ filtering impedance.
    *   **Resonance Damping:** Unlike standard inductors (e.g., $100\,\mu\text{H}$), its high resistive component at high frequencies ($> 30\,\text{MHz}$) absorbs and dissipates noise as heat, preventing anti-resonance peaks on $V_{DDA}$.

---

## 🔌 Connectors & Terminal Blocks

### 16. Weidmüller 1845040000 (Analog Terminal Block)
*   **Series:** OMNIMATE Signal LM 3.50.
*   **Specs:** 4-pole PCB terminal block, 3.50 mm pitch, 90° entry, screw clamping connection.
*   **Application:** J41 for 0-10V Analog inputs.

### 17. JST B2B-EH-A(LF)(SN) (CAN Connectors)
*   **Specs:** 2-pole shroud header, 2.50 mm pitch, top entry.
*   **Application:** J42 for CAN bus physical connections.

### 18. KRJ-CB4.2GYZNL (RJ45 Ethernet Jack)
*   **Role:** Ethernet connection with integrated magnetics and status LEDs.
*   **Details:** LED1 (Green) and LED2 (Yellow) connected via series resistors to the LAN8742A LED pins.

### 19. Molex 475900001 (Micro-USB Receptacle)
*   **Specs:** Micro-USB Type AB receptacle, bottom mount.

## See Also

*   [Design Technologies & Methods](../core/Technologies_Methods.md)
*   [CAN Interface](can-interface.md)
*   [Ethernet Interface](ethernet-interface.md)
*   [Analog Conditioning](analog-conditioning.md)
*   [USB Design](usb-design.md)
*   [SPI Protocol](SPI_Protocol.md)
