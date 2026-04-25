# Microcontroller Specifications: STM32F746ZG

Dense technical data for the STM32F746ZG microcontroller in the LQFP144 package.

## 🏗 Core Architecture
- **CPU:** ARM® 32-bit Cortex®-M7 with FPU.
- **Max Frequency:** 216 MHz.
- **Performance:** 462 DMIPS.
- **Memory:** Up to 1 MB Flash, 320 KB SRAM (including 64 KB of data TCM RAM and 16 KB of instruction TCM RAM).

## ⚡ Electrical Characteristics
| Parameter | Value Range | Notes |
| :--- | :--- | :--- |
| **V_DD** | 1.7 V to 3.6 V | Main digital supply. |
| **V_DDA** | 1.7 V to 3.6 V | Analog supply (Must be $\ge$ V_DD). |
| **V_BAT** | 1.65 V to 3.6 V | Backup supply (RTC, Backup RAM). |
| **V_DDUSB** | 1.7 V to 3.6 V | Dedicated USB supply (can be independent). |
| **I/O Level** | 3.3 V CMOS | Most pins are 5V tolerant (FT). |

## 📍 Pin Mapping (LQFP144)
Based on project requirements and Nucleo-F746ZG reference.

### Debug & Programming (SWD)
- **PA13:** SWDIO
- **PA14:** SWCLK
- **NRST:** System Reset (with 100nF capacitor and button).

### Communications
- **Ethernet (RMII):**
    - **PA1:** RMII_REF_CLK (50 MHz from PHY).
    - **PA2:** RMII_MDIO
    - **PC1:** RMII_MDC
    - **PA7:** RMII_CRS_DV
    - **PC4:** RMII_RXD0
    - **PC5:** RMII_RXD1
    - **PG11:** RMII_TX_EN
    - **PG13:** RMII_TXD0
    - **PG14:** RMII_TXD1
- **USB OTG FS:**
    - **PA11:** USB_DM
    - **PA12:** USB_DP
    - **PA9:** USB_VBUS (Sensing)
    - **PA10:** USB_ID
- **CAN Bus:**
    - **CAN1:** PD0 (RX), PD1 (TX).
    - **CAN2:** PB5 (RX), PB13 (TX).
    - *Note:* Transceivers (e.g., TJA1051) require VCC=5V and VIO=3.3V.
- **SPI Flash (SPI2):**
    - **PB10:** SCK
    - **PC2:** MISO
    - **PC3:** MOSI
    - **PB12:** CS (NSS)

### Analog & User I/O
- **Analog In (0-10V Scaled):** 
    - **PA3:** ADC_CH1 (via 22k/10k divider + buffer).
    - **PC0:** ADC_CH2 (via 22k/10k divider + buffer).
- **User LED:** PB0 (Green), PB7 (Blue), PB14 (Red).
- **User Button:** PC13.
- **Oscillator:** PH0 (OSC_IN), PH1 (OSC_OUT) - 8 MHz Crystal.

## 🚀 Boot Configuration
The boot mode is determined by the **BOOT0** pin and the **BOOT_ADDx** option bytes.

- **BOOT0 Pin:**
    - **Low (0):** Boot from the address defined in `BOOT_ADD0` (Default: Flash at 0x0800 0000).
    - **High (1):** Boot from the address defined in `BOOT_ADD1` (Default: System Memory / Bootloader at 0x1FF0 0000).
- **PB2 Pin (BOOT1):** Not physically used for boot selection in the STM32F7 series. It is available as a general-purpose GPIO.

### Expansion Connector (16 GPIOs)
Populated with available GPIOs on a 100 mil pitch header:
- **Port C:** PC8, PC9, PC10, PC11, PC12.
- **Port B:** PB1, PB2, PB11, PB15.
- **Port E:** PE2, PE3, PE4, PE5, PE6.
- **Port D:** PD2, PD3.

[Source: stm32f746zg.pdf, dm00244518.pdf, Project Proposal]
