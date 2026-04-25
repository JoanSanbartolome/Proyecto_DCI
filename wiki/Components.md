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
| **SO** | **PC2** | MISO |
| **SI** | **PC3** | MOSI |
| **SCK** | **PB10** | Clock |

### 🛠 Integration Requirement
- Tie **WP#** and **HOLD#** to 3.3V.
- 100nF decoupling capacitor on VDD.
- 10k $\Omega$ pull-up on **CE#**.

[Source: outputs/SPI_Flash_Characteristics.md]
