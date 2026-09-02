# CAN Interface (Controller Area Network)

Comprehensive guide for the implementation of dual CAN bus interfaces using the STM32F746ZG and SN65HVD230Q transceivers.

![System Architecture (Block Diagram)](../../images/Pasted%20image%2020260526170854.png)
## 🏗 System Architecture (Block Diagram)
The design uses a hierarchical structure to implement two identical CAN nodes:
`[STM32F746ZG bxCAN] <--- Logic (RX/TX 3.3V) ---> [SN65HVD230Q Transceiver] <--- Differential (CANH/CANL) ---> [Termination & TVS] <--- Connector]`

## 📍 Pin Mapping & Signal Types

### 1. MCU to Transceiver Interface
The STM32F746ZG provides two CAN controllers (CAN1 and CAN2).

| Controller | Signal | MCU Pin | Function |
| :--- | :--- | :--- | :--- |
| **CAN1** | **CAN1_RX** | **PD0** | Receive data from bus. |
| **CAN1** | **CAN1_TX** | **PD1** | Transmit data to bus. |
| **CAN2** | **CAN2_RX** | **PB5** | Receive data from bus. |
| **CAN2** | **CAN2_TX** | **PB6** | Transmit data to bus. |

### 2. Transceiver Pinout (SN65HVD230Q)
The SN65HVD230Q is a 3.3V CAN transceiver, eliminating the need for level shifters.

| Pin | Name | Type | Description |
| :--- | :--- | :--- | :--- |
| **1** | **D** | I | Transmit Data Input (Connects to MCU TX). |
| **2** | **GND** | P | Ground. |
| **3** | **VCC** | P | 3.3V Supply. |
| **4** | **R** | O | Receive Data Output (Connects to MCU RX). |
| **5** | **Vref** | O | $V_{CC}/2$ Reference voltage for bias. |
| **6** | **CANL** | AIO | CAN Bus Low-level I/O. |
| **7** | **CANH** | AIO | CAN Bus High-level I/O. |
| **8** | **RS** | I | Mode selection: GND=High Speed, $R_{ext}$=Slope, 3.3V=Standby. |

## 📐 Layout & Routing Considerations

### 1. Differential Pair (CANH/CANL)
- **Impedance:** **120 $\Omega$ differential** impedance.
- **Symmetry:** Route CANH and CANL as a tightly coupled differential pair. Minimize track length differences.
- **Proximity:** Place the TVS diode array (e.g., PESD2CAN) as close to the connector as possible.

### 2. Termination Network
The project supports optional line termination:
- **Standard:** $120 \Omega$ between CANH and CANL.
- **Split Termination (Recommended):** Two $60 \Omega$ resistors in series with a $4.7\text{ nF}$ capacitor from the center node to GND. This improves EMI performance.
- **Center Bias:** The center node of the split termination can be optionally biased using the **Vref** (Pin 5) for improved common-mode stability.

### 3. Slope Control (RS Pin)
- **EMI Reduction:** To reduce electromagnetic interference at lower bitrates, connect Pin 8 to GND via a resistor ($R_{ext}$).
- **Value:** $10\text{ k}\Omega$ ($\sim 15\text{ V/µs}$) to $100\text{ k}\Omega$ ($\sim 2\text{ V/µs}$). A $33\text{ k}\Omega$ resistor is standard for $500\text{ kbps}$ operation.

## 🛡 ESD Protection
See [CAN Bus Protection](../protection/can-protection.md) for full ESD design guidelines and TVS selection.
Uses SOT-23 TVS diodes:
- **Pin 1:** CANH
- **Pin 2:** CANL
- **Pin 3:** GND
- **Layout:** Route the bus signals *through* the TVS pads to eliminate stubs.

### 3. Logic Signals (MCU to Transceiver)
- **Signal Type:** 3.3V CMOS (RX/TX).
- **Speed:** Relatively low (up to 1 Mbps). These signals do not require specific differential routing or strict length matching.
- **Noise Immunity:** Although low speed, avoid routing RX/TX lines parallel to the high-speed CAN bus lines (CANH/CANL) or other switching sources (e.g., Ethernet RMII) for long distances to prevent crosstalk.
- **Drive Strength:** Ensure the MCU's GPIO output speed is set to "Medium" to provide clean edges without excessive ringing.

## 🛠 Altium Designer: Design Rules & Directives
To automate layout validation and ensure signal integrity, incorporate the following into your Altium project:

### 1. Differential Pair Directive
- **Setup:** Place a **Differential Pair Directive** (`Place > Directives > Differential Pair`) on the CANH and CANL nets in the schematic.
- **Naming:** Ensure nets are named with a `_P` and `_N` suffix (e.g., `CAN_BUS_P` and `CAN_BUS_N`).
- **PCB Rule:** Altium will automatically create a Differential Pair routing rule. Set the **Target Impedance** to **120 $\Omega$**.

### 2. Schematic Parameter Set (Directives)
Use a **Blanket** or **Parameter Set** directive to apply specific rules to the CAN block:
- **Net Class:** Group CANH/CANL into a net class called `CAN_BUS`.
- **Clearance Rule:** Set a minimum clearance of **0.25 mm** (10 mil) for the CAN bus nets to separate them from logic planes.
- **Width Rule:** Use a track width that meets the target impedance (calculated via Altium's Layer Stack Manager).

### 3. Polygon Pour (GND)
- **Isolation:** Ensure a solid GND plane underneath the transceiver and the logic signals.
- **Vias:** Use at least two GND vias for the transceiver's GND pin (Pin 2) and the TVS diode's GND pin to minimize parasitic inductance.

[Source: sn65hvd230q-q1.pdf, STM32_Esqumaticos.pdf, Design Best Practices]

## See Also

- [CAN Bus Protection](../protection/can-protection.md) — Detailed ESD and overvoltage protection routing and placement.
