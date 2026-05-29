# Hardware Architecture: STM32F746ZG PCB

Design strategies for power, clocking, and signal integrity.

## ⚡ Power Delivery Network (PDN)
The STM32F746ZG requires multiple stable voltage rails.

### Decoupling Requirements
- **VDD Pins:** Each VDD pin requires a 100 nF ceramic capacitor.
- **Bulk Capacitance:** 1x 4.7 µF ceramic capacitor on the VDD rail.
- **VDDA:** 100 nF + 1 µF close to pin. Use a ferrite bead for isolation from VDD.
- **VCAP:** 2x 2.2 µF low-ESR ceramic capacitors (VCAP_1, VCAP_2) to VSS. **CRITICAL.**
- **Peripheral Power:** Most peripherals (USB, CAN, SPI Flash) operate on the **3.3V rail**. Ensure LDO can handle the combined peak current.

## 🕒 Clock Distribution
- **HSE:** 8 MHz crystal on PH0/PH1.
- **LSE:** 32.768 kHz crystal on PC14/PC15.
- **Ethernet:** 50 MHz RMII_REF_CLK sourced from PHY to PA1.

## 🛡 Signal Integrity & Layout
Detailed guidelines for complex interfaces are maintained in their respective articles:
- **USB:** See [USB Design Guidelines](peripherals/usb-design.md) ($90 \Omega$, ESD).
- **CAN:** See [CAN Interface](../peripherals/can-interface.md) ($120 \Omega$, dual hierarchy).
- **SPI Protocol:** See [SPI Protocol](SPI_Protocol.md) ($33 \Omega$, drive strength).
- **Ethernet:** See [Ethernet Interface](ethernet-interface.md) ($100 \Omega$ diff, LAN8742A, RMII).

- **Analog (0-10V):** 
    - Use a **22k / 10k** resistor divider to scale 10V to 3.125V.
    - Implement an **Op-Amp Buffer** (e.g., TLV9002) for each channel to ensure high input impedance and protect MCU pins.
- **Manual Boot Selection:**
    - Use a **10k $\Omega$ pull-down** on BOOT0 for default Flash boot.
    - Add a **push-button** to 3.3V (with a 100 $\Omega$ series resistor) to manually enter the Bootloader.

## 📐 PCB Stack-up
- **Layers:** 4 layers (Signal / GND / PWR / Signal).
- **Thickness:** Max **1.6 mm** (Lab Circuits).
- **Mounting:** 4x 3.5mm holes connected to **EARTH**.

[Source: stm32f746zg.pdf, dm00244518.pdf, Project Proposal]
