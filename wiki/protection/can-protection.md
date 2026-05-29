# CAN Bus Protection

> Sources: Design Decisions 2026-05-18

## Overview
ESD protection for the CAN bus interfaces using dedicated TVS diodes in SOT-23 packages.

## 🛡️ Protection Strategy
- **Component:** SOT-23 CAN TVS (e.g., NUP2105L, PESD2CAN).
- **Placement:** Immediately adjacent to the CAN connector.

## 📐 Layout & Routing Guidelines
### 1. Pin Configuration (SOT-23)
- **Pin 1:** CAN_H
- **Pin 2:** CAN_L
- **Pin 3:** GND (Main Plane)

### 2. Signal Integrity
- **Flow-Through Routing:** The CANH and CANL differential pair should pass directly over Pins 1 and 2 of the SOT-23 package.
- **No Stubs:** Do not use branching traces. Stubs cause reflections and increase inductance, reducing ESD clamping performance.
- **Grounding:** Connect Pin 3 to the GND plane using a high-current via (or two smaller ones) to ensure a low-impedance path for discharge.

### 3. Connection Order
1. `[Connector]`
2. `[TVS Protection]`
3. `[Common Mode Choke]` (Optional/Recommended)
4. `[Termination Network]` (Split Termination)
5. `[CAN Transceiver]` (SN65HVD230Q)

## See Also
- [CAN Interface](../peripherals/can-interface.md)
- [Design Review 2026-05-18](../core/Design_Review_2026-05-18.md)

[Source: Design Review 2026-05-18]
