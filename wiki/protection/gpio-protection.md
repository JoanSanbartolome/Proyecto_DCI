# GPIO and USART Protection

Guidelines for protecting STM32F746ZG I/O pins exposed on expansion connectors against ESD and overvoltage.

## 🛡️ Protection Strategy
External connectors are the primary entry point for ESD and electrical overstress. A multi-layer protection strategy is required.

### 1. ESD Suppression (Primary)
Use dedicated TVS diode arrays placed immediately after the connector.
- **Component:** ESDA6V1BC6 (4-channel bidirectional).
- **Placement:** As close to the connector as possible.
- **Connection:** Pins 2 and 5 to a solid GND plane. Pins 1, 3, 4, 6 to the signal lines.
- **Characteristics:** 6.1V breakdown protects 3.3V/5V tolerant pins without interference.

### 2. Series Current Limiting (Secondary)
Place resistors between the TVS diode and the MCU pin to limit current during transients or shorts.
- **Value:** 47 $\Omega$ to 100 $\Omega$.
- **Function:** Works with internal MCU clamping diodes to limit $I_{IN}$.
- **USART Note:** For high baud rates (>1 Mbps), use lower values (22-47 $\Omega$) to maintain signal integrity.

### 3. Voltage Tolerance
Always verify pin type in the datasheet.
- **FT (5V Tolerant):** Can safely handle 5V logic.
- **TTa (3.3V Max):** Connected to ADC, will be destroyed by 5V.
- **Reference:** See `wiki/core/Microcontroller.md` for pin assignments.

## 📐 Layout Rules
1. **Order:** `[Connector]` -> `[TVS Diode]` -> `[Series Resistor]` -> `[MCU Pin]`.
2. **GND:** Use short, wide traces (or vias) for TVS ground pins to minimize inductance.
3. **Filtering:** For slow signals (buttons), add a 100 pF capacitor to GND after the resistor.

## Sources
- STMicroelectronics, ESDA6V1BC6 Datasheet, 2004.
- STMicroelectronics, STM32F746ZG Datasheet.

## Raw
- [ESDA6V1BC6 Datasheet Summary](../../raw/protection/2026-05-17-esda6v1bc6-datasheet-summary.md)
