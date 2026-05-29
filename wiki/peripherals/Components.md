# Component Selection: SPI Flash

Summary of the selected SPI Flash IC for the project.

## 💾 Selected Part: SST25VF040B
- **Density:** 4 Mbit (Exceeds project requirement of 2 Mbit).
- **Interface:** SPI (up to 50 MHz).
- **Voltage:** 2.7V - 3.6V (Matches 3.3V system rail).
- **Package:** SOIC-8 (Recommended for prototyping/hand soldering).

### 📍 STM32F746ZG Connections
| Flash Pin | MCU Pin (SPI2) | Function |
| :--- | :--- | :--- |
| **CE#** | **PB12** | Chip Select |
| **SO** | **PB14** | MISO |
| **SI** | **PB15** | MOSI |
| **SCK** | **PB10** | Clock |

### 🛠 Integration Requirement
- Tie **WP#** and **HOLD#** to 3.3V.
- 100nF decoupling capacitor on VDD.
- 10k $\Omega$ pull-up on **CE#**.

[Source: outputs/SPI_Flash_Characteristics.md]

## 📡 Selected Part: SN65HVD230QDRG4Q1
- **Function:** CAN Transceiver.
- **Supply:** 3.3V (Eliminates the need for 5V rail for CAN).
- **Speed:** Up to 1 Mbps.
- **Features:** Automotive qualified (AEC-Q100), slope control for EMI reduction.

> **Obsolescence Note:** The TJA1051 (5V) previously considered in early logs was **discarded** in favor of the SN65HVD230Q (3.3V) to simplify power delivery and ensure direct MCU compatibility.

## 🛡️ Selected Part: USBLC6-4SC6
- **Function:** 4-channel ESD Protection.
- **Application:** Ethernet (2 pairs) and high-speed I/O.
- **Capacitance:** 3.5 pF typ.
- **Package:** SOT23-6L.

## 🔌 Selected Part: Weidmüller 1845040000
- **Function:** 4-pole PCB Terminal Block.
- **Series:** OMNIMATE Signal LM 3.50.
- **Specs:** 3.5mm pitch, 90° entry, screw connection.
- **Application:** 0-10V Analog Inputs.

## 🎛️ Selected Part: OPA350EA/250
- **Function:** High-Speed Analog Buffer.
- **Supply:** 3.3V (Single supply).
- **Package:** Micro6 (SOT-23).
- **Application:** Analog Input Conditioning (U5).

## ⚡ Selected Part: LD39050PU33R
- **Function:** 3.3V Low-Dropout Regulator.
- **Current:** 500 mA.
- **Package:** DFN-6.
- **Application:** USB Power Regulation (U4).
