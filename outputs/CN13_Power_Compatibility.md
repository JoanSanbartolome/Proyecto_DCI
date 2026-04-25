# CN13 Connector Power Compatibility Analysis

## ❓ Question
Can the USB CN13 connector be used as a power input for the Nucleo-144 (STM32F746ZG) board? If not, what changes are needed?

## 🚫 Answer: NO
According to the **User Manual UM1974 (dm00244518.pdf)**, the USB Micro-AB connector (**CN13**) **cannot** power the Nucleo-144 board.

### ⚠️ Critical Warning
Section 7.10 of UM1974 states:
> "USB Micro-AB connector (CN13) cannot power the Nucleo-144 board. To avoid damaging the STM32, it is mandatory to power the Nucleo-144 before connecting a USB cable on CN13. Otherwise there is a risk of current injection on STM32 I/Os."

## 🛠️ Required Changes for Custom PCB Design
If you are designing a custom PCB and want to use the USB connector as a power input, you must implement the following hardware changes:

1.  **VBUS Connection:** Connect the **VBUS** pin from the USB connector (CN13 equivalent) to the board's **5V power rail**.
2.  **Back-powering Protection:** Use a **Schottky diode** (low forward voltage drop) or a **Power Distribution Switch** (e.g., ST890 or a P-Channel MOSFET circuit) between VBUS and the internal 5V rail. This prevents the board from back-powering a host computer when multiple power sources (like an external 12V supply) are connected.
3.  **Voltage Regulation:** Ensure the 5V from USB passes through a **3.3V LDO regulator** (like the LD1117S33 used in reference designs) to provide the VDD rail for the STM32.
4.  **Decoupling & Protection:**
    *   Add a **10 µF tantalum/ceramic bulk capacitor** and a **100 nF ceramic capacitor** on the VBUS line.
    *   Include **ESD protection** (e.g., USBLC6-2SC6) on the D+, D-, and VBUS lines.
5.  **Current Handling:** Standard USB 2.0 ports provide up to 500mA. Ensure your board's total consumption (including peripherals) does not exceed this limit, or use a USB-C PD controller for higher power requirements.

[Source: dm00244518.pdf, Section 7.4 & 7.10]
