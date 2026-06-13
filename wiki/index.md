# STM32F746ZG PCB Wiki Index

Welcome to the central knowledge base for the STM32F746ZG PCB design project. This wiki synthesizes technical documentation into actionable design data.

## 📂 Core Design
| Article | Summary | Updated |
|---------|---------|---------|
| [Project Requirements](core/Project_Requirements.md) | Design goals and constraints. | 2026-04-22 |
| [Design Review 2026-05-18](core/Design_Review_2026-05-18.md) | **[CRITICAL]** Schematic validation findings and error checklist. | 2026-05-18 |
| [Microcontroller Specifications](core/Microcontroller.md) | Pinouts, electrical characteristics, and peripherals. | 2026-04-22 |
| [Hardware Architecture](core/Hardware_Architecture.md) | Power delivery, clocks, decoupling, and stackup (4L, 1.6mm, Clase 5, Lab Circuits). | 2026-06-13 |
| [Reference Designs](core/Reference_Designs.md) | Insights from the Nucleo-F746ZG board. | 2026-04-22 |
| [GPIO Pin Configuration](core/gpio-pin-configuration.md) | [Archived] Comprehensive classification of GPIO pins by voltage tolerance and functions. | 2026-05-24 |
| [Design Technologies & Methods](core/Technologies_Methods.md) | Technologies and methodologies used for signal integrity and noise coupling. | 2026-06-10 |
| [Pre-Production Audit 2026-06-10](core/Design_Audit_2026-06-10.md) | **[CRITICAL]** Full pre-production audit: 2 critical, 1 major, 11 minor issues (CAN RS, CAN, USB, VDDA, PDN & VBAT verified). Board NOT cleared. | 2026-06-10 |
| [PDN Decoupling Theory](core/PDN_Decoupling_Theory.md) | Physical and mathematical theory of decoupling and bulk capacitance. | 2026-06-10 |
| [Via Stitching & GND Guard Rings](core/Via_Stitching_GND_Guard_Rings.md) | GND return-path stitching strategy: zones, via geometry (drill 0.3/pad 0.68 mm, Clase 5), λ/20 spacing, AGND/GND boundary, and crystal oscillator guard rings (HSE X42 + LSE X41). | 2026-06-13 |
| [Manufacturing Constraints](core/Manufacturing_Constraints.md) | Lab Circuits DFM: fabrication class determination (Clase 5), AR=5.33 conflict, annular ring analysis, corrected via pad to 0.68 mm, full parameter tables for Classes 3–7. | 2026-06-13 |

## 🛡️ Protection
| Article | Summary | Updated |
|---------|---------|---------|
| [GPIO Protection](protection/gpio-protection.md) | ESD and overvoltage protection for external I/O pins. | 2026-05-17 |
| [Ethernet Protection](protection/ethernet-protection.md) | USBLC6-4SC6 layout and differential pair protection. | 2026-05-18 |
| [CAN Bus Protection](protection/can-protection.md) | SOT-23 TVS placement and grounding for CAN bus. | 2026-05-18 |

## 🔌 Peripherals
| Article | Summary | Updated |
|---------|---------|---------|
| [USB Design](peripherals/usb-design.md) | Guidelines for 90Ω differential impedance and power. | 2026-05-14 |
| [CAN Interface](peripherals/can-interface.md) | SN65HVD230Q logic, dual node hierarchy, and 120Ω routing. | 2026-05-26 |
| [Analog Conditioning](peripherals/analog-conditioning.md) | 0-10V scaling, RMII crosstalk mitigation, and Op-Amp buffers. | 2026-05-24 |
| [Ethernet Interface](peripherals/ethernet-interface.md) | RMII pin mapping, PHY pinout, and 100Ω differential routing. | 2026-05-26 |
| [SPI Protocol](peripherals/SPI_Protocol.md) | Implementation guidelines: pinout, frequency limits, termination physics, layout rules, and open issues MIN-SPI-01/02. | 2026-06-13 |
| [Component Selection](peripherals/Components.md) | Database of selected ICs and their roles. | 2026-06-10 |

## 📚 Theory & Course Materials
| Article | Summary | Updated |
|---------|---------|---------|
| [PCB Design Course Index](theory/PCB_Design_Course_Index.md) | Thematic index of the 25 PCB Design course reference documents (Altium, SI, PDN, DFM, EMC). | 2026-06-13 |
| [SPI Signal Integrity Theory](theory/SPI_Signal_Integrity_Theory.md) | Deep theory: electrical length criterion, lumped LC models, series termination physics, crosstalk, and layout rules applied to SPI2 + SST25VF040B. | 2026-06-13 |

## 🛠 Latest Updates
- **2026-06-13**: Ingest de `Lab Circuits - Tablas de parámetros.pdf`: creado [Manufacturing Constraints](core/Manufacturing_Constraints.md) con análisis DFM completo. **3 conflictos identificados:** AR=5.33 (requiere Clase 5), corona externa insuficiente (0.15 mm < 0.17 mm), corona interna insuficiente. Correción: pad de via stitching de 0.6 mm → **0.68 mm**. Cascade updates en Via Stitching, Hardware Architecture.
- **2026-06-13**: Ingest de `Articulo_altium_SPI_SI.md` (Altium, Z. Peterson, 2026-02-17): creado artículo de teoría profunda [SPI Signal Integrity Theory](theory/SPI_Signal_Integrity_Theory.md) y expandido masivamente [SPI Protocol](peripherals/SPI_Protocol.md). Cascade updates en Ethernet Interface y PCB Course Index.
- **2026-06-13**: Via Stitching & GND Guard Rings: plan completo de cosido selectivo (MCU, LDO, PHY, cristales) + guard rings de 10 vias alrededor de HSE X42 y LSE X41. Regla λ/20, doble via en bulk caps, frontera AGND/GND y procedimiento Altium.
- **2026-06-10**: Ingested and structured the PCB Design Course Index mapping the 25 newly added theoretical reference documents.
- **2026-06-10**: Corrected CAN slope control (MAJ-CAN-02), CAN filter capacitor (CRI-CAN-01), USB ESD (CRI-USB-01), and VDDA ferrite bead (CRI-ANA-01) in the pre-production audit.
- **2026-06-10**: Verified LDO output decoupling (MAJ-PDN-01) and VBAT decoupling (MIN-ANA-02) as false positives, and corrected VDD bulk capacitance distribution (MIN-PDN-02) in the pre-production audit.
- **2026-06-10**: Added theoretical article on PDN decoupling, trace inductance, and bulk capacitance.
- **2026-06-10**: **[CRITICAL]** Pre-production design audit completed. 2 critical issues block production, 2 from previous review still unfixed.
- **2026-06-10**: Updated components database and documented applied design technologies and methods from schematic prints.
- **2026-05-26**: Added comprehensive Ethernet and CAN Interface documentation.
- **2026-05-18**: Documented Ethernet/CAN protection and 0-10V Analog conditioning.
- **2026-05-17**: Added GPIO/USART protection guidelines based on ESDA6V1BC6.
- **2026-05-17**: Reorganized wiki into topic-based subdirectories (Karpathy style).
- **2026-05-14**: Added USB and CAN transceiver design guidelines.

---
*Last updated: 2026-06-13*
