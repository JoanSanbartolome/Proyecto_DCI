# Manual Boot Selection Circuitry: STM32F746ZG

This document describes how to implement a manual boot selection circuit using a push-button, allowing the user to toggle between normal Flash boot and System Bootloader mode.

## 💡 Circuit Design
To provide manual control over the boot mode, the **BOOT0** pin must be biased using a pull-down resistor and connected to a high-voltage source (3.3V) through a switch.

### 🧩 Components
- **Push-button (S1):** Tactile switch connected between **3.3V** and the **BOOT0** network.
- **Pull-down Resistor (R1):** **10k $\Omega$** resistor connected between **BOOT0** and **GND**. This ensures the default state is Logic 0 (Flash Boot).
- **Current Limiting Resistor (R2):** **100 $\Omega$ to 1k $\Omega$** resistor in series between the switch and the BOOT0 pin. This protects the MCU pin against transients and potential accidental short circuits.

### 📐 Schematic Layout
```text
3.3V
 |
[ ] (S1 Push-button)
 |
 +-------[ R2 100R ]------- MCU BOOT0 (Pin 138)
 |
[R1 10k]
 |
GND
```

## 🕹 Operation
1. **Normal Operation (Button Open):** 
   - R1 pulls BOOT0 to GND.
   - MCU boots from **Flash Memory** (default user application).
2. **Manual Bootloader Entry (Button Pressed during Reset):**
   - Button connects BOOT0 to 3.3V (Logic 1).
   - User must press and hold S1 while toggling the **RESET** button.
   - MCU enters **System Bootloader** mode (allows programming via UART, USB, etc.).

## ⚠️ Design Considerations
- **Hysteresis:** The STM32F746ZG BOOT0 pin has built-in hysteresis (~10% VDD), which helps prevent false triggering due to noise.
- **Debouncing:** Hardware debouncing is generally not required for BOOT0 since its state is only sampled during the reset sequence.
- **Stability:** Place the 10k $\Omega$ pull-down physically close to the MCU pin to minimize noise pickup on the high-impedance trace.

[Source: stm32f746zg.pdf Section 6.3.17, dm00244518.pdf Section 7.14]
