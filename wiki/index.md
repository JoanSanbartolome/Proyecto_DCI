# STM32F746ZG PCB Wiki Index

Welcome to the central knowledge base for the STM32F746ZG PCB design project. This wiki synthesizes technical documentation into actionable design data.

## 📂 Core Design
| Article | Summary | Updated |
|---------|---------|---------|
| [Project Requirements](core/Project_Requirements.md) | Design goals and constraints. | 2026-04-22 |
| [Design Review 2026-05-18](core/Design_Review_2026-05-18.md) | **[CRITICAL]** Schematic validation findings and error checklist. | 2026-05-18 |
| [Microcontroller Specifications](core/Microcontroller.md) | Pinouts, electrical characteristics, and peripherals. | 2026-04-22 |
| [Hardware Architecture](core/Hardware_Architecture.md) | Power delivery, clocks, and decoupling. | 2026-05-14 |
| [Reference Designs](core/Reference_Designs.md) | Insights from the Nucleo-F746ZG board. | 2026-04-22 |
| [GPIO Pin Configuration](core/gpio-pin-configuration.md) | [Archived] Comprehensive classification of GPIO pins by voltage tolerance and functions. | 2026-05-24 |
| [Design Technologies & Methods](core/Technologies_Methods.md) | Technologies and methodologies used for signal integrity and noise coupling. | 2026-06-10 |
| [Pre-Production Audit 2026-06-10](core/Design_Audit_2026-06-10.md) | **[CRITICAL]** Full pre-production audit: 5 critical, 2 major, 11 minor issues (PDN & VBAT verified). Board NOT cleared. | 2026-06-10 |
| [PDN Decoupling Theory](core/PDN_Decoupling_Theory.md) | Physical and mathematical theory of decoupling and bulk capacitance. | 2026-06-10 |

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
| [SPI Protocol](peripherals/SPI_Protocol.md) | Implementation guidelines and PCB routing rules for SPI bus. | 2026-05-24 |
| [Component Selection](peripherals/Components.md) | Database of selected ICs and their roles. | 2026-06-10 |

## 🛠 Latest Updates
- **2026-06-10**: Verified LDO output decoupling (MAJ-PDN-01) and VBAT decoupling (MIN-ANA-02) as false positives, and corrected VDD bulk capacitance distribution (MIN-PDN-02) in the pre-production audit.
- **2026-06-10**: Added theoretical article on PDN decoupling, trace inductance, and bulk capacitance.
- **2026-06-10**: **[CRITICAL]** Pre-production design audit completed. 5 critical issues block production, 4 from previous review still unfixed.
- **2026-06-10**: Updated components database and documented applied design technologies and methods from schematic prints.
- **2026-05-26**: Added comprehensive Ethernet and CAN Interface documentation.
- **2026-05-18**: Documented Ethernet/CAN protection and 0-10V Analog conditioning.
- **2026-05-17**: Added GPIO/USART protection guidelines based on ESDA6V1BC6.
- **2026-05-17**: Reorganized wiki into topic-based subdirectories (Karpathy style).
- **2026-05-14**: Added USB and CAN transceiver design guidelines.

---
*Last updated: 2026-06-10*
