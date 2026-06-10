# Ethernet Protection (USBLC6-4SC6)

> Sources: ST USBLC6-4SC6 Datasheet; Design Decisions 2026-05-18

## Overview
High-speed ESD protection for the 10/100 Ethernet interface using a 4-channel TVS diode array.

## 🛡️ Component: USBLC6-4SC6
- **Package:** SOT23-6L.
- **Capacitance:** 3.5 pF (I/O to GND), suitable for high-speed differential pairs.
- **Channels:** 4 independent channels (protects 2 differential pairs).

## 📐 Layout & Routing Guidelines
To maintain signal integrity and maximize ESD shunting efficiency:

### 1. Pin Mapping (2 Pairs)
| Signal | Pin | Function |
| :--- | :--- | :--- |
| **TX+** | **1** | I/O 1 |
| **GND** | **2** | Ground (Direct Via to Plane) |
| **TX-** | **3** | I/O 2 |
| **RX-** | **4** | I/O 3 |
| **VCC** | **5** | 3.3V (with 100nF Decoupling) |
| **RX+** | **6** | I/O 4 |

### 2. Differential Pair Routing
- **Flow-Through Simulation:** Route the differential traces directly over the pads. Avoid "T" junctions or stubs.
- **Symmetry:** When the pair "opens" to reach Pins 1/3 or 6/4, ensure the deviation is perfectly symmetrical for both hilos (+ and -) to maintain 100 $\Omega$ differential impedance.
- **Proximity:** Place the TVS as close as possible to the RJ45 connector, ideally before the magnetics for connector-side protection.

### 3. Grounding
- Connect Pin 2 to the main GND plane using a large via (or multiple small vias) placed immediately adjacent to the pad to minimize parasitic inductance.
- Avoid long traces for the GND connection; inductance reduces the clamping effectiveness during fast ESD transients.

## See Also
- [Ethernet Interface](../peripherals/ethernet-interface.md)
- [Design Review 2026-05-18](../core/Design_Review_2026-05-18.md)

[Source: Design Review 2026-05-18]
