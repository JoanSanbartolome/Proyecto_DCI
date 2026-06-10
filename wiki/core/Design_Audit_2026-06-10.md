# Pre-Production Design Audit — STM32F746ZG Custom PCB

> Sources: Joan San Bartolome, 2026-06-10; STMicroelectronics, 2016-02-15; Microchip, 2015-08-01; Texas Instruments, 2003-09-12
> Raw: [STM32_Esqumaticos.pdf](../../raw/prints/STM32_Esqumaticos.pdf); [STM32_Layout.pdf](../../raw/prints/STM32_Layout.pdf); [stm32f746zg.pdf](../../raw/datasheets/stm32f746zg.pdf); [dm00244518.pdf](../../raw/datasheets/dm00244518.pdf); [sn65hvd230q-q1.pdf](../../raw/datasheets/sn65hvd230q-q1.pdf)

## Overview

This document constitutes a **formal pre-production design audit** of the STM32F746ZG custom PCB (Project DCI 2025/2026, Rev B-01). The review has been conducted by examining the complete schematic set (9 sheets) against the project requirements, relevant datasheets, and industry best practices for mixed-signal embedded systems.

The audit is structured by subsystem. Each section opens with a status verdict, lists what is correct, and then details every deficiency found — ranked by severity — together with a concrete remediation proposal.

**Severity Scale:**
- 🔴 **CRITICAL** — Board will not function or will be damaged. Blocks production.
- 🟠 **MAJOR** — Degraded performance, intermittent failures, or EMC non-compliance. Must fix before production.
- 🟡 **MINOR** — Sub-optimal but functional. Fix recommended.
- ✅ **PASS** — Compliant with requirements and best practices.

---

## 1. Power Delivery Network (PDN)

**Status: ✅ PASS (Corrected)**

### ✅ What Is Correct
- The LD39050PU33R LDO (U4) is a solid choice for a 500 mA linear regulator. The DFN-6 package provides excellent thermal performance.
- Input decoupling (C13 = 1µF) and output decoupling (C14 = 1µF, C15 = 100nF, C16 = 100nF) are present, satisfying stability requirements.
- The USB VBUS path includes the BAT60JFILM Schottky diode (D44) for reverse-current protection and the MF-MSMF050-2 resettable fuse (F41) for overcurrent protection. This is textbook correct.
- The PG (Power Good) pin is correctly used to drive a status LED (LD2) via a transistor switch (T1, 9013 NPN).

### 🟢 MAJ-PDN-01: LDO Output Stability [VERIFICADO / FALSO POSITIVO]

**Finding:** In the initial audit, it was flagged that the output side of the LD39050PU33R had only 100nF capacitors (C15, C16), violating the 1µF minimum. However, a detailed schematic review confirms that **C14 (1µF X5R 0603)** is already placed on the LDO output rail (`P3V3_PER`), in parallel with C15 and C16.

**Risk:** None. The combination of C14 (1µF) + C15 (100nF) + C16 (100nF) provides a total of 1.2µF, which fully satisfies the datasheet stability requirements.

**Correction/Status:** ✅ No action required. The schematic already implements the correct output capacitance. C16 remains at 100nF.

### 🟢 MIN-PDN-02: No Dedicated Bulk Capacitor near MCU VDD Cluster [CORREGIDO]

**Finding:** The MCU has 13× 100nF decoupling capacitors (C51-C64) and a single 4.7µF bulk capacitor (C64). The 4.7µF capacitor is placed at pin VDD_9 (Pin 95).

**Risk:** During simultaneous high-speed switching on Ethernet RMII + CAN + SPI, the instantaneous current demand can exceed what a single 4.7µF capacitor can supply without significant voltage droop.

**Recommendation:**
- Add a second **10µF X5R 0805** bulk capacitor near VDD_1 (Pin 72), at the opposite end of the VDD pin cluster. This distributes the bulk energy storage across both halves of the package.
- **Status:** ✅ Corregido. Se ha añadido un capacitor de bulk de 10µF X5R 0805 en Pin 72. Ver justificación en [PDN Decoupling Theory](PDN_Decoupling_Theory.md).

### ✅ VCAP Configuration: PASS

The VCAP pins (VCAP1 at Pin 71, VCAP2 at Pin 106) are correctly terminated with 2.2µF X7R capacitors (C48, C49) directly to VSS. This is critical for the internal core voltage regulator and is implemented correctly.

---

## 2. Analog Power Domain (VDDA / VREF+)

**Status: ✅ PASS (Corrected)**

### ✅ What Is Correct
- VDDA (Pin 33) and VREF+ (Pin 32) are supplied through a ferrite bead (L41) from VDD, with local decoupling (C59 = 100nF).
- VSSA (Pin 31) is connected to the analog ground (AGND) reference.
- The intent to isolate the analog supply from digital noise is correctly identified.

### 🟢 CRI-ANA-01: Ferrite Bead Value Excessively High (100µH) [CORREGIDO]

**Finding:** L41 was specified as a "100µH BEAD" (which was an inductor in place of a ferrite bead).

**Risk:** High DCR causes DC voltage drop on VDDA/VREF+ (corrupting ADC readings), and high inductance creates a low-frequency resonant peak with the decoupling capacitors that amplifies noise.

**Correction:**
- Replace L41 with a proper **ferrite bead** rated at $Z = 120\,\Omega\text{ @ }100\,\text{MHz}$, $\text{DCR} < 100\,\text{m}\Omega$, $I_{rated} \geq 300\,\text{mA}$ (e.g., **Murata BLM18PG121SN1D**, 0603 package).
- Add a **1µF X7R 0603** capacitor in parallel with the existing 100nF on VDDA (total: 100nF + 1µF) to meet STM32 decoupling guidelines.
- **Status:** ✅ Corregido. Se ha montado la perla de ferrita **Murata BLM18PG121SN1D** en L41 y se ha añadido el capacitor de 1µF en paralelo con C59 para cumplir con la especificación de desacoplo de VDDA.

### 🟢 MIN-ANA-02: VBAT Decoupling [VERIFICADO / FALSO POSITIVO]

**Finding:** In the initial audit, it was flagged that Pin 6 (VBAT) was connected to VDD with a 100nF capacitor (C55) through an RC network formed by R59 and R61. However, a detailed schematic review shows that Pin 6 (VBAT) is actually connected directly to a **1µF decoupling capacitor (C50)** to VDD. The resistors R59 and R61 are adjacent on the sheet but not electrically connected to VBAT.

**Risk:** None. Since there is no coin cell battery or supercapacitor, the RTC calendar is lost on power down, which is acceptable if backup timekeeping is not required. The decoupling is fully adequate.

**Correction/Status:** ✅ No action required. The pin is decoupled with a 1µF capacitor and has no series/pull-up resistors connected.

---

## 3. Clock System

**Status: 🟡 MINOR ISSUES**

### ✅ What Is Correct
- **HSE (8 MHz):** NX3225GD-8.000M crystal (X42) on PH0/PH1 with matched 4.3 pF load capacitors (C45, C47). This is consistent with the crystal's specified load capacitance.
- **LSE (32.768 kHz):** NX3215SA crystal (X41) on PC14/PC15 with C41 and C42 marked `4.3pF [N/A]`, indicating they are footprints placed for optional tuning but not populated. This is acceptable if the crystal's specifications allow operation with only parasitic PCB capacitance (~2 pF).
- **Ethernet PHY (25 MHz):** X53T-C20SSA-25.000MHz crystal (X4) on XTAL1/XTAL2 of the LAN8742A, with 30 pF load capacitors (C6, C11). Values match the crystal spec.

### 🟡 MIN-CLK-01: Missing Feedback Resistor on HSE

**Finding:** There is no feedback resistor across the HSE crystal (X42, pins PH0/PH1). The schematic shows the crystal with load capacitors but without the 1 MΩ feedback resistor specified in AN2867 (ST Oscillator Design Guide).

**Risk:** The STM32F746ZG has an internal feedback resistor in most configurations, so the oscillator will likely start. However, for production reliability across temperature extremes (-40°C to +85°C) and crystal aging, an external **1 MΩ resistor across PH0-PH1** is recommended by ST's application note AN2867 to guarantee reliable startup.

**Recommendation:**
- Add a **1 MΩ 0402** resistor footprint (DNP by default) across PH0 and PH1 for production margin. Populate only if startup issues arise during environmental testing.

### ✅ Ethernet REF_CLK: PASS

The 50 MHz RMII reference clock is sourced from the LAN8742A (Pin 14, nINT/REFCLK0) and routed to PA1. This is the standard clocking topology for RMII mode. The PHY generates the clock from its 25 MHz crystal and provides it to the MCU, avoiding the need for an external 50 MHz oscillator.

---

## 4. Ethernet Subsystem (LAN8742A + RMII + RJ45)

**Status: 🟠 MAJOR ISSUES**

### ✅ What Is Correct
- The LAN8742A PHY (U9) is correctly wired to the MCU via RMII with the standard 9-signal pinout.
- The RBIAS resistor (R14 = 12.1kΩ 1%) is correctly placed on Pin 24.
- The RJ45 with integrated magnetics (KRJ-CB4.2GYZNL, CN14) eliminates the need for external magnetics and simplifies the layout.
- ESD protection (USBLC6-4SC6, U10) is correctly placed on the MDI differential pairs.
- Status LEDs are connected via series resistors (R67 = 270Ω, R65 = 270Ω) to the PHY LED pins.
- The MODE pins (RXD0/MODE0 on Pin 8, RXD1/MODE1 on Pin 7) are pulled to the correct states via the RMII signal termination resistors during power-on reset to configure the PHY in RMII mode.

### 🟠 MAJ-ETH-01: Incorrect RMII Signal Termination Resistor Values

**Finding:** The RMII data lines from the MCU to the PHY include series resistors R7 = 33Ω and R8 = 33Ω on RXER (Pin 10) and on the RXD lines. Additionally, R40 = 33Ω is placed on CRS_DV and R17 = 33Ω on REF_CLK.

**Risk:** A 33Ω series termination on a 50Ω trace creates an impedance mismatch. The driver output impedance of the STM32F746ZG GPIO at "Very High Speed" is approximately 25-40Ω. Adding 33Ω in series yields a total source impedance of 58-73Ω driving a 50Ω line, which introduces:
- **Overshoot and undershoot** at the receiver end (up to 15% of $V_{OH}$).
- **Marginal setup/hold timing** at 50 MHz, where the timing budget is only 10 ns per clock period.

**Correction:**
- For RMII at 50 MHz, the **recommended series termination is 22Ω** (matching $Z_0 - R_{driver} \approx 50 - 30 = 20\Omega$, rounded to the nearest standard value).
- Change R7, R8, R40, and R17 from **33Ω to 22Ω**.
- **Exception:** R14 (RBIAS) must remain at 12.1kΩ. Do not confuse with signal resistors.

### 🟠 MAJ-ETH-02: RXER Pin (Pin 10) Not Properly Handled

**Finding:** In the schematic, Pin 10 (RXER/PHYAD0) of the LAN8742A is connected through a $10\,\text{k}\Omega$ pull-down (R9). During normal RMII operation, this pin is the **Receive Error** output from the PHY.

**Risk:** The RXER signal is **not connected to the MCU**. In RMII mode, the STM32F746ZG MAC does not require RXER (it uses CRS_DV and RXD[1:0] only). However, the pull-down on RXER also sets the **PHYAD0** strap during power-on reset. With R9 = 10kΩ to GND, PHYAD0 = 0, setting the PHY address to **0x00**.

**Issue:** PHY address 0x00 is the MDIO broadcast address. While many RMII implementations function correctly at address 0, some network stacks may encounter issues when scanning for PHYs. The LAN8742A default address with all MODE/PHYAD pins at default is **0x00**, which is technically valid but not ideal.

**Recommendation:**
- This is acceptable for a single-PHY design. However, if future revisions may add a second PHY, change R9 to a **pull-up to 3.3V** to set PHYAD0 = 1 (PHY address = 0x01), reserving address 0 for broadcast.

### 🟡 MIN-ETH-03: Missing Series Resistors on MDI Differential Pairs

**Finding:** The MDI pairs (TX+/TX-, RX+/RX-) from the PHY to the magnetics section have series resistors R1, R2 = 50Ω and R5, R6 = 50Ω, but also R10-R13 = 75Ω in what appears to be a Bob-Smith termination network.

**Risk:** The 75Ω resistors (R10-R13) are connected to a center-tap arrangement for common-mode noise rejection. This is a well-known Ethernet best practice. However, the center tap must be terminated to **chassis ground (EARTH)** through a high-voltage capacitor (typically 1kV, 2.2nF), not to digital GND. Verify on the layout that these connect to the mounting holes / chassis, not the PCB signal ground.

**Recommendation:**
- Confirm on the PCB layout that the Bob-Smith termination center tap connects to EARTH (mounting holes) through a 2.2nF / 2kV Y-class capacitor. If it connects to digital GND, the common-mode rejection is defeated and EMC testing will fail.

---

## 5. USB Interface

**Status: 🔴 CRITICAL ISSUE (Previously Identified)**

### ✅ What Is Correct
- Micro-USB connector (Molex 475900001, CN44) correctly wired with VBUS, D+, D-, ID, GND, and Shield.
- VBUS sensing on PA9 for USB OTG detection.
- BAT60JFILM Schottky diode (D44) for reverse-current protection.
- MF-MSMF050-2 resettable fuse (F41) on VBUS.
- USB_ID on PA10 for OTG role detection.

### 🔴 CRI-USB-01: Wrong ESD Protection Component (ESDA6V1BC6 instead of USBLC6-4SC6)

**Finding:** The schematic shows D45 as **ESDA6V1BC6** on the USB D+/D- lines. This was flagged in the previous [Design Review 2026-05-18](Design_Review_2026-05-18.md) but the schematic prints dated 2026-06-10 **still show the ESDA6V1BC6**.

**Risk:** The ESDA6V1BC6 has a line capacitance of approximately **15-20 pF per channel**. USB 2.0 Full Speed requires <10 pF total bus capacitance to maintain signal edge rates within spec. At 20 pF, the rise/fall times will be degraded beyond the 4-20 ns specification window, causing:
- Intermittent enumeration failures.
- Data CRC errors under heavy throughput.
- Complete failure of USB High Speed (480 Mbps) if ever attempted.

**This was previously identified as a critical error and has NOT been corrected.**

**Correction:**
- Replace D45 (ESDA6V1BC6) with **USBLC6-4SC6** (3.5 pF per channel). Pin-compatible in SOT-23-6L package. Requires no board re-spin if the footprint matches.

### 🟡 MIN-USB-02: Missing D+ Pull-Up Resistor for Full Speed Detection

**Finding:** The USB D+ line has no pull-up resistor to 3.3V. The STM32F746ZG has an **internal pull-up** that can be enabled via software (`USB_OTG_FS->GCCFG |= USB_OTG_GCCFG_PWRDWN`).

**Risk:** If the firmware fails to enable the internal pull-up, the USB host will not detect the device. The internal pull-up is also subject to process variation (1.5kΩ ± 20%).

**Recommendation:**
- Add a **1.5kΩ ±5%** resistor footprint (DNP) from D+ to 3.3V as a fallback. This is not strictly required if the firmware is reliable, but provides a hardware guarantee for production testing before firmware is loaded.

---

## 6. CAN Bus Interface (×2)

**Status: 🟠 MAJOR ISSUE**

### ✅ What Is Correct
- Dual SN65HVD230QDRG4Q1 transceivers (U6 ×2) correctly wired with 3.3V logic, eliminating the need for level shifting.
- CAN1: PD0 (RX) / PD1 (TX). CAN2: PB5 (RX) / PB6 (TX). Pin assignments are free of alternate function conflicts.
- PESD2CANFD24V-TR (D42, D43) ESD protection placed at the connector side.
- JST B2B-EH-A connectors (J41, J42) for the bus connection.
- Split termination network: 2× 60Ω (R29, R30) with a capacitor (C18) to GND.
- RS pin (Pin 8) connected to GND for high-speed mode.

### 🔴 CRI-CAN-01: Common-Mode Filter Capacitor Value Error

**Finding:** C18 = **4.7 pF**. This was flagged in the previous [Design Review 2026-05-18](Design_Review_2026-05-18.md) with the correction to change to 4.7 nF. The schematic prints dated 2026-06-10 **still show 4.7 pF**.

**Risk:** The split termination capacitor ($2 \times 60\,\Omega + C_{CM}$) forms a common-mode low-pass filter. The cutoff frequency is:

$$f_c = \frac{1}{2\pi \cdot R \cdot C} = \frac{1}{2\pi \cdot 60 \cdot 4.7 \times 10^{-12}} \approx 564\,\text{MHz}$$

At 564 MHz, this capacitor provides **zero common-mode noise filtering**. The bus will be dominated by common-mode emissions in the 1-30 MHz range (where automotive/industrial EMC limits are strictest), causing EMC certification failure.

With $C = 4.7\,\text{nF}$:
$$f_c = \frac{1}{2\pi \cdot 60 \cdot 4.7 \times 10^{-9}} \approx 564\,\text{kHz}$$

This places the cutoff safely below the CAN bit rate while providing strong common-mode rejection above 1 MHz.

**This was previously identified as a critical error and has NOT been corrected.**

**Correction:**
- Change C18 from **4.7 pF** to **4.7 nF** (X7R, 50V, 0402 or 0603).

### 🟠 MAJ-CAN-02: RS Pin Grounded Without Slope Control Option

**Finding:** Both transceivers have Pin 8 (RS) connected directly to GND.

**Risk:** With RS = GND, the transceiver operates in **high-speed mode** with maximum slew rate (~50 V/µs). This maximizes EMI emissions. In an industrial environment or enclosed product, this may cause:
- Radiated emission failures during EMC testing (EN 61000-6-4).
- Susceptibility to ringing on long bus cables (>2 m).

**Correction:**
- Replace the direct GND connection with a **0Ω resistor footprint** (0402). Populate with 0Ω for high-speed mode. If EMC testing reveals issues, swap to 10kΩ-100kΩ for slope control without a board re-spin.
- Document the BOM option: `R_RS = 0Ω (default) / 33kΩ (EMI fallback)`.

### 🟡 MIN-CAN-03: Missing Common-Mode Choke

**Finding:** No common-mode choke (CMC) is present between the TVS protection and the transceiver for either CAN channel.

**Risk:** For cables exceeding 1 meter in a noisy industrial environment, a CMC significantly improves common-mode rejection and reduces radiated emissions. Without it, the bus relies entirely on the split termination and the transceiver's internal common-mode rejection.

**Recommendation:**
- Add a footprint for a **CMC** (e.g., Würth 744232090, $Z_{CM} = 90\,\Omega\text{ @ }100\,\text{MHz}$, $Z_{DM} < 1\,\Omega$) between the TVS and the split termination on each channel. Populate as needed based on EMC testing results.

---

## 7. Analog Input Conditioning (0-10V, ×2)

**Status: 🔴 CRITICAL ISSUE (Previously Identified)**

### ✅ What Is Correct
- The hierarchical sheet approach (`ADC.SchDoc` instanced via `Repeat(CANAL, 1, 2)`) ensures identical conditioning for both channels.
- The OPA350EA/250 is a good choice: Rail-to-Rail I/O, 38 MHz GBW, low offset (150 µV max), single-supply.
- Anti-aliasing RC filter ($R_{20} = 1\,\text{k}\Omega$, $C_{12} = 100\,\text{nF}$, $f_c \approx 1.6\,\text{kHz}$) is well-dimensioned for industrial sensors.
- The Weidmüller 1845040000 terminal block provides robust screw-clamp connections.
- The TSV631AILT (U41) with R47 = 200kΩ, R48 = 10kΩ, R49 = 510Ω appears to form an indicator/status circuit, separate from the signal path.

### 🔴 CRI-ADC-01: Resistor Divider Scaling Error

**Finding:** The schematic shows R18 = **100kΩ** and R19 = **220kΩ**. This was flagged in the [Design Review 2026-05-18](Design_Review_2026-05-18.md) as producing a node voltage of:

$$V_{out} = V_{in} \times \frac{R_{19}}{R_{18} + R_{19}} = 10\,\text{V} \times \frac{220\text{k}}{100\text{k} + 220\text{k}} = 6.875\,\text{V}$$

This exceeds the 3.3V supply rail and the OPA350's absolute maximum input voltage ($V_{DD} + 0.3\,\text{V} = 3.6\,\text{V}$).

**Risk:** At 6.875V, the OPA350 input protection diodes will forward-bias, injecting current into the 3.3V rail. This will:
1. Latch the Op-Amp into an undefined state.
2. Potentially back-power the 3.3V rail through the protection diodes, raising the supply above 3.3V and corrupting every device on the bus.
3. Cause permanent degradation or destruction of the OPA350 under sustained overvoltage.

**This was previously identified as a critical error and has NOT been corrected.**

**Correction:**
- Change R18 and R19 to achieve a safe scaling factor. Target: $V_{out,max} = 3.0\,\text{V}$ at $V_{in} = 10\,\text{V}$ (leaves 300 mV headroom below the 3.3V rail):

$$\frac{R_{19}}{R_{18} + R_{19}} = \frac{3.0}{10.0} = 0.3$$

- **Option A (recommended):** R18 = **22kΩ**, R19 = **10kΩ**. $V_{out} = 10 \times 10/(22+10) = 3.125\,\text{V}$. Input impedance = 32kΩ (adequate for industrial sensors, >10kΩ output impedance is rare).
- **Option B (higher impedance):** R18 = **220kΩ**, R19 = **100kΩ**. $V_{out} = 10 \times 100/(220+100) = 3.125\,\text{V}$. Input impedance = 320kΩ. Higher impedance means more susceptibility to noise pickup; mitigate with shorter traces and shielding.

### 🔴 CRI-ADC-02: Unused Op-Amp Channel Not Configured

**Finding:** The OPA350EA/250 (U5) is a dual-channel device in an MSOP-8/SOT-23-8 package. The second channel (U5B) has its inputs marked as NC (No Connect) in the schematic.

**Risk:** A floating input on any operational amplifier — especially a high-bandwidth device like the OPA350 (38 MHz GBW) — will:
1. Self-oscillate at or near the gain-bandwidth product frequency.
2. Generate high-frequency noise that couples into the adjacent channel (U5A) through the shared supply pins.
3. Draw excessive supply current (potentially 5-10× quiescent), stressing the analog supply rail.

**This was previously identified as a critical error and has NOT been corrected.**

**Correction:**
- Configure U5B as a unity-gain buffer with its input tied to a known DC potential:
  - Connect U5B **non-inverting input (Pin 5)** to **AGND** (via a 1kΩ resistor to prevent noise injection).
  - Connect U5B **output (Pin 7)** directly to **inverting input (Pin 6)**.
  - This forces U5B to output a stable 0V (AGND) with minimal current draw.

### 🟡 MIN-ADC-03: ADC Pin Assignment Discrepancy

**Finding:** The wiki ([Microcontroller.md](Microcontroller.md)) states the analog inputs are on **PA3** (ADC_CH1) and **PC0** (ADC_CH2). However, the schematic's connector sheet shows the ADC module outputs connecting to **PB0** and **PB1** (ADC12_IN8 / ADC12_IN9) through the conditioning circuit, while the [analog-conditioning.md](../peripherals/analog-conditioning.md) article references PB0/PB1 as the MCU inputs.

**Risk:** If the wiki documentation is out of sync with the schematic, firmware development will target the wrong pins, requiring last-minute pin remapping or, worse, a board re-spin.

**Recommendation:**
- **Verify and reconcile:** Open the schematic (Sheet 4, Connectors) and trace the physical net from the Op-Amp output through the RC filter to the MCU pin. Update the wiki to match the actual schematic net assignment. The schematic is the source of truth.

---

## 8. SPI Flash Memory (SST25VF040B)

**Status: 🟡 MINOR ISSUES**

### ✅ What Is Correct
- The SST25VF040B (U7) is correctly wired: CE# (Pin 1), SO (Pin 2), WP# (Pin 3), VSS (Pin 4), SI (Pin 5), SCK (Pin 6), HOLD# (Pin 7), VDD (Pin 8).
- 10kΩ pull-up (R31) on CE# line for default deselection.
- 100nF decoupling capacitor (C21) on VDD.
- Series termination resistor (R32 = 33Ω) on SCK to dampen reflections.
- WP# and HOLD# are tied to VDD (3.3V), disabling write protection and hold functions.

### 🟡 MIN-SPI-01: Missing Series Resistor on MOSI (SI) Line

**Finding:** Only the SCK line has a series termination resistor (R32 = 33Ω). The MOSI (SI) and MISO (SO) lines have no series termination.

**Risk:** At SPI clock speeds above 25 MHz, the MOSI line can exhibit ringing and overshoot at the flash IC input. The MISO line is driven by the flash and terminated by the MCU's internal impedance, so it is less critical.

**Recommendation:**
- Add a **33Ω 0402** resistor in series with the MOSI line (SI, Pin 5), placed close to the MCU output pin (PB15). This matches the SCK termination strategy and ensures clean edges at the flash input.

### 🟡 MIN-SPI-02: SPI Bus Exposed on Port B Alongside RMII

**Finding:** SPI2 uses PB10 (SCK), PB12 (CS), PB14 (MISO), PB15 (MOSI). These pins are on Port B, which also hosts PB13 (RMII_TXD1). On the LQFP-144 package, PB13 (Pin 74) is physically between PB12 (Pin 73) and PB14 (Pin 75).

**Risk:** RMII_TXD1 is a 50 MHz digital signal. Its physical adjacency to the SPI MISO line (PB14) creates a potential crosstalk path. While this is a layout concern more than a schematic issue, the pin assignment makes it impossible to avoid adjacency.

**Recommendation:**
- On the PCB layout, route RMII_TXD1 (PB13) on a different layer than the SPI signals where they emerge from the MCU, and ensure a solid GND reference plane between them.
- Consider adding a GND guard trace between PB13 and PB14 on the signal layer.

---

## 9. MCU Configuration & Boot

**Status: ✅ PASS (with notes)**

### ✅ What Is Correct
- BOOT0 (Pin 138) is tied to GND through a resistor (R58 = 100Ω) with a pull-down (R60 = 100Ω) and a push-button (B43) to 3.3V for manual bootloader entry. This is correct.
- NRST (Pin 25) has a 100nF filter capacitor (C43) and a push-button (B42, Black) for manual reset. This meets the datasheet recommendation.
- The user button (B41, Blue) is on PC13 with a series resistor (R62 = 330Ω) and is connected to USR_BTN. This is standard.
- PDR_ON (Pin 143) is connected directly to VDD, enabling the internal power-down reset. This is correct for normal operation.

### 🟡 MIN-MCU-01: User LEDs Conflict with SPI/RMII

**Finding:** The wiki states PB14 is assigned as "User LED (Red)". However, PB14 is also assigned as SPI2_MISO. Looking at the schematic, PB14 has a 100Ω resistor (R54 = 33Ω actually per the resistor label, but the LED assignment from the wiki is a documentation error).

**Risk:** If PB14 is truly dual-assigned (LED + SPI MISO), the LED load (typically 5-10 mA through a series resistor) will pull the MISO line and corrupt SPI data during flash operations.

**Recommendation:**
- **Verify the schematic net:** If PB14 is used for SPI MISO, do **not** connect an LED to it. Reassign the Red LED to a free GPIO (e.g., PE1, which appears to be used for a Green LED via R49 = 510Ω). Remove the LED documentation from PB14 in the wiki.

---

## 10. GPIO Extension Connector & ESD Protection

**Status: ✅ PASS**

### ✅ What Is Correct
- The 18×2 male header (CN41) exposes P5V, P3V3, and 16 GPIOs for external interfacing.
- 4× ESDA6V1BC6 arrays (D46, D47, D48, D49) provide ESD protection on all GPIO lines, placed between the connector and the MCU.
- The SWD connector (CN42, 6×1 header) includes TCK, TMS, SWO, NRST with 22Ω series resistors (R43, R44, R45, R46) for signal integrity and current limiting.
- The BAT60JFILM diode (D41) on the P5V line provides reverse-polarity protection.

### 🟡 MIN-GPIO-01: Schematic Annotation "MANTENER LO MAS CERCA POSIBLE DEL CONECTOR EN EL RUTADO Y EN PARALELO"

**Finding:** There is a design note on Sheet 4 instructing the layout engineer to place the ESD protection components as close to the connector as possible and route in parallel.

**Risk:** None. This is correct practice. The note is well-placed. However, formal design directives should be captured in the **Altium constraint system** (Design Rules), not as free-text annotations that can be missed.

**Recommendation:**
- Create a **Component Group** in Altium for the ESD protection components and set a **proximity constraint** (max distance from connector = 5 mm) as a formal DRC rule.

---

## 11. Decoupling Strategy & Component Summary

**Status: 🟡 MINOR ISSUES**

### ✅ What Is Correct
- 13× 100nF ceramic capacitors (C51-C63) on each VDD/VSS pin pair of the MCU.
- 1× 4.7µF bulk capacitor (C64) on VDD.
- 2× 2.2µF X7R (C48, C49) on VCAP pins.
- 1× 1µF X5R (C50) on BYPASS_REG (Pin 38).
- Separate AGND/GND domains with star grounding.

### 🟡 MIN-DEC-01: Capacitor Dielectric Not Specified for 100nF

**Finding:** All 100nF capacitors are listed as "100nF" without dielectric specification. In the BOM, these should be **X7R or X5R** dielectric to maintain effective capacitance under DC bias.

**Risk:** If the procurement team sources C0G/NP0 (which is acceptable for 100nF but expensive) or, worse, Y5V/Z5U (which can lose >80% capacitance at 3.3V DC bias), the decoupling effectiveness will be severely compromised.

**Recommendation:**
- Specify **X7R, 16V, 0402 or 0603** for all 100nF decoupling capacitors in the BOM. Add a BOM note: "No Y5V or Z5U substitutions permitted."

---

## 12. Summary of Outstanding Issues from Previous Design Review

The following items were identified in the [Design Review 2026-05-18](Design_Review_2026-05-18.md) and remain **uncorrected** in the schematic prints dated 2026-06-10:

| ID | Issue | Severity | Status |
|:---|:------|:---------|:-------|
| CRI-USB-01 | ESDA6V1BC6 on USB D+/D- (should be USBLC6-4SC6) | 🔴 CRITICAL | **NOT FIXED** |
| CRI-CAN-01 | C18 = 4.7 pF (should be 4.7 nF) | 🔴 CRITICAL | **NOT FIXED** |
| CRI-ADC-01 | R18/R19 divider outputs 6.87V (should be ≤3.125V) | 🔴 CRITICAL | **NOT FIXED** |
| CRI-ADC-02 | U5B floating inputs (unused Op-Amp channel) | 🔴 CRITICAL | **NOT FIXED** |

> [!CAUTION]
> **These four issues MUST be resolved before sending the design to the PCB fabrication house.** Any one of them individually is sufficient to render the board non-functional or cause permanent component damage on first power-up.

---

## 13. Complete Issue Tracker

| ID | Subsystem | Severity | Description | Action |
|:---|:----------|:---------|:------------|:-------|
| CRI-ANA-01 | Analog Power | 🔴 | L41 = 100µH inductor instead of ferrite bead on VDDA | Replace with BLM18PG121SN1D (120Ω @ 100MHz). Add 1µF cap. |
| CRI-USB-01 | USB | 🔴 | ESDA6V1BC6 on USB D+/D- (20 pF) | Replace D45 with USBLC6-4SC6 (3.5 pF). |
| CRI-CAN-01 | CAN Bus | 🔴 | C18 = 4.7 pF common-mode filter | Change to 4.7 nF X7R. |
| CRI-ADC-01 | Analog Input | 🔴 | Divider outputs 6.87V to 3.3V Op-Amp | Change to R18=22kΩ, R19=10kΩ. |
| CRI-ADC-02 | Analog Input | 🔴 | U5B inputs floating (oscillation) | Wire U5B as unity-gain buffer to AGND. |
| MAJ-PDN-01 | Power | 🟠 | LDO output capacitance below datasheet minimum | Replace C16 with 1µF X5R. |
| MAJ-ETH-01 | Ethernet | 🟠 | RMII series termination = 33Ω (should be 22Ω) | Change R7, R8, R17, R40 to 22Ω. |
| MAJ-ETH-02 | Ethernet | 🟠 | PHYAD0 set to broadcast address 0x00 | Acceptable for single-PHY. Document. |
| MAJ-CAN-02 | CAN Bus | 🟠 | RS pin hard-grounded, no slope control option | Replace GND wire with 0Ω resistor footprint. |
| MIN-PDN-02 | Power | 🟡 | Single bulk capacitor for MCU VDD cluster | Add 10µF near VDD_1 (Pin 72). |
| MIN-ANA-02 | Analog Power | 🟡 | VBAT unnecessary resistors | Remove R59, R61 if RTC not used. |
| MIN-CLK-01 | Clocks | 🟡 | Missing 1MΩ feedback resistor on HSE | Add DNP footprint across PH0-PH1. |
| MIN-ETH-03 | Ethernet | 🟡 | Bob-Smith termination center-tap grounding | Verify connects to EARTH, not digital GND. |
| MIN-USB-02 | USB | 🟡 | No external D+ pull-up fallback | Add 1.5kΩ DNP footprint D+ to 3.3V. |
| MIN-CAN-03 | CAN Bus | 🟡 | No common-mode choke on CAN bus | Add CMC footprint (DNP by default). |
| MIN-ADC-03 | Analog Input | 🟡 | Wiki/schematic pin assignment discrepancy for ADC | Reconcile documentation. |
| MIN-SPI-01 | SPI Flash | 🟡 | No series resistor on MOSI line | Add 33Ω on PB15-SI. |
| MIN-SPI-02 | SPI Flash | 🟡 | PB13 (RMII) adjacent to PB14 (MISO) crosstalk risk | Layer separation + GND guard in layout. |
| MIN-MCU-01 | MCU Config | 🟡 | PB14 dual-assigned (LED + SPI MISO) in wiki | Verify and reconcile. |
| MIN-DEC-01 | Decoupling | 🟡 | 100nF capacitor dielectric not specified | Specify X7R in BOM. |
| MIN-GPIO-01 | GPIO/ESD | 🟡 | Layout note as free text instead of DRC rule | Create Altium proximity constraint. |

---

## 14. Verdict

**The board is NOT cleared for production in its current state.**

There are **5 critical issues** (🔴) that will cause hardware damage or complete functional failure. Four of these were identified in the previous design review (2026-05-18) and remain unaddressed. This is unacceptable for a design approaching production.

**Mandatory actions before fabrication:**
1. Fix all 5 critical issues (CRI-ANA-01, CRI-USB-01, CRI-CAN-01, CRI-ADC-01, CRI-ADC-02).
2. Fix all 3 major issues (MAJ-PDN-01, MAJ-ETH-01, MAJ-CAN-02).
3. Regenerate schematic prints and submit for a **second review** before proceeding.

The 13 minor issues (🟡) should be addressed during the revision cycle but do not individually block production.

## See Also

- [Design Review 2026-05-18](Design_Review_2026-05-18.md)
- [Hardware Architecture](Hardware_Architecture.md)
- [Component Selection](../peripherals/Components.md)
- [Design Technologies & Methods](Technologies_Methods.md)
- [Ethernet Interface](../peripherals/ethernet-interface.md)
- [CAN Interface](../peripherals/can-interface.md)
- [USB Design](../peripherals/usb-design.md)
- [Analog Conditioning](../peripherals/analog-conditioning.md)
- [SPI Protocol](../peripherals/SPI_Protocol.md)
- [GPIO Protection](../protection/gpio-protection.md)
