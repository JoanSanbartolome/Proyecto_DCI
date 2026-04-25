# Reference Designs: Nucleo-F746ZG Insights

Key design patterns extracted from the Nucleo-144 board to ensure functional compatibility.

## 🌐 Ethernet Layout
The Nucleo-F746ZG uses a specific strategy for the Ethernet interface:
- **PHY Interface:** RMII (Reduced Media Independent Interface) is used to save pins.
- **Magnetic Isolation:** The RJ45 connector requires an internal or external magnetic.
- **Copper Clearance:** **CRITICAL.** All copper layers must be removed underneath the magnetic component to prevent noise coupling and ensure HV isolation.
- **REF_CLK:** The PHY provides the 50MHz clock to the MCU's PA1 pin.

## 🔌 USB Implementation
- **VBUS Management:** Use a power switch (e.g., ST890 or similar) to control VBUS when acting as a Host.
- **Power Input (CN13):** 
    - **WARNING:** On the Nucleo board, the User USB (CN13) **cannot** be used as a power input. Connecting it without prior power risks MCU damage due to current injection.
    - **Custom Design Fix:** To use USB as a power source, connect VBUS to the 5V rail through a **Schottky diode** or power switch for protection.
- **Protection:** USBLC6-4SC6 or ESDA6V1BC6 for ESD protection on D+/D- lines.

## 🛠 Debugging (SWD)
- The Nucleo board integrates an ST-LINK/V2-1.
- For this custom PCB, the ST-LINK is removed. A **10-pin 1.27mm or 2.54mm header** is used for SWD (PA13, PA14, NRST, VCC, GND).
- **NRST:** Include a 100 nF capacitor and a reset button.

## 💡 Solder Bridges (SB)
The Nucleo board uses solder bridges to multiplex pins. In the custom design:
- Hard-wire the connections to the desired peripherals (Ethernet, USB, CAN).
- Do not include the solder bridges unless testing multiple configurations is required.

[Source: dm00244518.pdf]
