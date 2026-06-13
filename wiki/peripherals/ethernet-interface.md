# Ethernet Interface (RMII & LAN8742A)

Comprehensive guide for the 10/100 Ethernet implementation using the STM32F746ZG MAC and the LAN8742A PHY.

## 🏗 System Architecture (Block Diagram)
The Ethernet subsystem follows a standard 10/100 implementation:
`[STM32F746ZG MAC] <--- RMII (50MHz) ---> [LAN8742A PHY] <--- MDI (Diff Pair) ---> [External Magnetics] <--- RJ45 Connector]`

## 📍 Pin Mapping & Signal Types

### 1. MCU to PHY Interface (RMII)
Reduced Media Independent Interface (RMII) reduces the pin count from 17 (MII) to 9.

| Signal Name | MCU Pin | Function |
| :--- | :--- | :--- |
| **RMII_REF_CLK** | **PA1** | 50 MHz reference clock (Sourced from PHY). |
| **RMII_MDIO** | **PA2** | Management Data Input/Output (SMI). |
| **RMII_MDC** | **PC1** | Management Data Clock (SMI). |
| **RMII_CRS_DV** | **PA7** | Carrier Sense / Data Valid. |
| **RMII_RXD0** | **PC4** | Receive Data 0. |
| **RMII_RXD1** | **PC5** | Receive Data 1. |
| **RMII_TX_EN** | **PG11** | Transmit Enable. |
| **RMII_TXD0** | **PG13** | Transmit Data 0. |
| **RMII_TXD1** | **PB13** | Transmit Data 1. |

### 2. PHY Pinout (LAN8742A-CZ-TR)
The LAN8742A is a high-performance 10/100 Ethernet Transceiver.

| Pin | Name | Type | Description |
| :--- | :--- | :--- | :--- |
| **5** | **XTAL1/CLKIN** | I | 25 MHz Crystal Input. |
| **4** | **XTAL2** | O | 25 MHz Crystal Output. |
| **14** | **nINT/REFCLK0**| O | 50 MHz RMII Reference Clock Output. |
| **15** | **nRST** | I | Active Low Reset. |
| **21** | **TXP** | AIO | Transmit Differential Pair (Positive). |
| **20** | **TXN** | AIO | Transmit Differential Pair (Negative). |
| **23** | **RXP** | AIO | Receive Differential Pair (Positive). |
| **22** | **RXN** | AIO | Receive Differential Pair (Negative). |
| **24** | **RBIAS** | I | External 12.1 kΩ 1% bias resistor to GND. |
| **25** | **GND_EP** | P | Exposed Pad - Must be soldered to GND plane. |

## 📐 Layout & Routing Considerations

### 1. Differential Pairs (MDI)
- **Impedance:** **100 $\Omega$ differential** impedance for TX and RX pairs.
- **Symmetry:** Traces must be length-matched and routed symmetrically to minimize EMI.
- **Protection:** Place the **USBLC6-4SC6** TVS diode array close to the RJ45 connector, before the magnetics.
- **Magnetics Isolation:** **CRITICAL.** Remove all copper in all layers underneath the magnetics component to ensure electrical isolation and prevent noise coupling.

### 2. RMII Signals
- **Impedance:** **50 $\Omega$ single-ended** impedance.
- **Clock (REF_CLK):** Route with special care as it is a 50 MHz signal. Minimize vias and avoid crossing plane splits.
- **Length Matching:** Ensure RMII data lines (TXD, RXD) and control lines (TX_EN, CRS_DV) are roughly the same length to meet timing requirements.
- **⚠️ Crosstalk Risk (MIN-SPI-02):** Signal **RMII_TXD1 (PB13, Pin 74)** is physically adjacent to **SPI2_MISO (PB14, Pin 75)** on the LQFP-144 package. Route these signals on different PCB layers or maintain ≥ 3× trace width separation if on the same layer. See [SPI Protocol](SPI_Protocol.md) for mitigation details.

### 3. Power & Grounding
- **Decoupling:** 100 nF capacitors on every VDD pin. 1 µF and 10 µF bulk capacitors near the PHY.
- **Analog Supply:** Filter VDD1A and VDD2A using ferrite beads if necessary.
- **GND:** Connect the PHY's exposed pad (Pin 25) directly to the main GND plane with multiple vias.

## 🛡 ESD Protection
The **USBLC6-4SC6** protects the two differential pairs:
- **Pins 1/3:** Connected to TX+/TX-.
- **Pins 6/4:** Connected to RX+/RX-.
- **Pin 2:** Ground.
- **Pin 5:** VCC (3.3V).

[Source: stm32f746zg.pdf, STM32_Esqumaticos.pdf, Propuesta+Diseño+DCI+25-26.pdf]

## See Also

- [SPI Protocol](SPI_Protocol.md) — Crosstalk entre RMII_TXD1 (PB13) y SPI2_MISO (PB14)
- [Design Audit 2026-06-10](../core/Design_Audit_2026-06-10.md) — MAJ-ETH-01, MIN-ETH-03
- [Ethernet Protection](../protection/ethernet-protection.md) — USBLC6-4SC6 layout
