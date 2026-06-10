# Wiki Log

## 🗓 2026-04-22
### Refined Ingestion & Synthesis
- **Detailed Component Research:**
    - Verified **CAN** transceiver requirements: VCC=5V, VIO=3.3V (e.g., TJA1051).
    - Identified standard **Analog Scaling** for 0-10V: 22k/10k divider for a 3.125V peak.
    - Confirmed **Ethernet RMII** pinout compatibility with Nucleo-144 layout.
    - Specified **SPI Flash** as standard 4-wire SPI (SST25VF040B) on SPI2.
- **Wiki Updates:**
    - Updated `Microcontroller.md` with refined pin assignments for CAN, SPI, and Analog.
    - Updated `Hardware_Architecture.md` with specific resistor values for analog scaling and transceiver power requirements.
    - Verified 4-layer stack-up and copper clearance rules in `Reference_Designs.md`.
- **Key Decisions:**
    - Selected **PD0/PD1** for CAN1 and **PB5/PB13** for CAN2 to avoid RMII/USB conflicts.
    - Recommended **Op-Amp buffers** for analog inputs to ensure signal integrity as per best practices.
- **Component Documentation:**
    - Documented **SST25VF040B** SPI Flash characteristics and integration rules.
    - Created `Components.md` to track specific IC selections.
    - Investigated **PB2/BOOT1** role; confirmed it is **not used** for boot in STM32F746 (replaced by Option Bytes).
- **Boot Strategy:**
    - Documented **Manual Boot Selection** circuit using a 10k pull-down and push-button to 3.3V for Bootloader access.
- **Interface Guidelines:**
    - Compiled **SPI Protocol** recommendations, focusing on series termination, pull-ups for CS, and high-speed layout (50 MHz).
    - Added **Advanced SPI** tuning: GPIO drive strength, software NSS management, and industrial robustness (filtering/isolation).

### 🗓 2026-04-22 (Continued)
- **USB Power Compatibility:**
    - Investigated **CN13 (User USB)** power capabilities. 
    - **Outcome:** Confirmed it **cannot** power the Nucleo board; doing so risks current injection and MCU damage.
    - **Documentation:** Created detailed analysis in `outputs/CN13_Power_Compatibility.md` and updated `wiki/Reference_Designs.md` with custom design fix (Schottky diode/Power switch).

### 🗓 2026-05-14
- **Peripherals Research & Ingestion:**
    - **USB Design:** Detailed analysis of differential impedance (90 $\Omega$), integrated matching on STM32F746, and VBUS power circuitry.
    - **CAN Transceiver:** Analyzed **SN65HVD230Q** datasheet (3.3V logic, slope control via Rs pin, and split termination).
- **Wiki Updates:**
    - Created `raw/peripherals/` and `wiki/peripherals/` directories to support topic-based knowledge management.
    - Ingested raw research into `raw/peripherals/2026-05-14-usb-impedance-power.md` and `raw/peripherals/2026-05-14-sn65hvd230q-can-transceiver.md`.
    - Compiled curated articles: `wiki/peripherals/usb-design.md` and `wiki/peripherals/can-transceiver.md`.
    - Updated `wiki/index.md` with the new **Peripherals** section.

### 🗓 2026-05-14 (Remediation)
- **Wiki Health Audit & Fixes:**
    - **Resolved Power Contradiction:** Updated `Hardware_Architecture.md` to specify 3.3V for CAN (was 5V).
    - **Consolidated Peripherals:** Replaced redundant layout rules in `Hardware_Architecture.md` with links to specific articles in `wiki/peripherals/`.
    - **Component Status:** Formally documented the shift from TJA1051 to SN65HVD230Q in `Components.md` and marked the former as obsolete.

## [2026-05-17] ingest | GPIO and USART Protection
- Created `raw/protection/2026-05-17-esda6v1bc6-datasheet-summary.md` from `raw/esda6v1bc6.pdf`.
- Created `wiki/protection/gpio-protection.md` with multi-layer protection strategy.
- Updated `wiki/index.md` with the new Protection section.

## [2026-05-18] ingest | Ethernet, CAN, and Analog Design
- Created: `wiki/protection/ethernet-protection.md`
- Created: `wiki/protection/can-protection.md`
- Created: `wiki/peripherals/analog-conditioning.md`
- Created: `wiki/core/Design_Review_2026-05-18.md` (Schematic Audit)
- Updated: `wiki/peripherals/Components.md`
- Updated: `wiki/index.md`

## [2026-05-26] ingest | Ethernet and CAN Interfaces
- Created: `wiki/peripherals/ethernet.md`
- Created: `wiki/peripherals/can-interface.md`
- Updated: `wiki/index.md`
- Updated: `wiki/core/Hardware_Architecture.md`
- Updated: `wiki/protection/ethernet-protection.md`
- Updated: `wiki/protection/can-protection.md`
- Updated: `wiki/core/Microcontroller.md` (Fixed RMII/CAN2 pin conflict)
- Deleted: `wiki/peripherals/can-transceiver.md` (Superseded)

## [2026-05-24] query | Archived: GPIO Pin Configuration

## [2026-05-24] ingest | PCB Layout: SPI2 Port B & RMII Crosstalk
- **SPI2 Pinout Optimization:** Reassigned SPI2 to Port B (PB10, PB12, PB14, PB15) to group all flash signals on the right side of the LQFP144 package.
- **SPI PCB Rules:** Established SCK as critical (no vias, solid GND) and set length matching to 10-15 mm.
- **Mixed-Signal Warning:** Documented crosstalk risk between PC5 (RMII) and PB0/PB1 (ADC).
- **ADC Conditioning Update:** Finalized **1.6 kHz Anti-aliasing filter** ($1k\Omega/100nF$) and added post-buffer assistance capacitor (10nF) for high-speed sampling.
- **Updated:** `wiki/peripherals/SPI_Protocol.md`
- **Updated:** `wiki/peripherals/analog-conditioning.md`

## [2026-06-10] lint | 8 issues found, 8 auto-fixed

## [2026-06-10] ingest | Planos de Esquemáticos y Layout (Componentes, Tecnologías y Métodos)
- **Ingesta de planos de diseño:** Analizados los archivos `STM32_Esqumaticos.pdf` y `STM32_Layout.pdf` añadidos a `raw/`.
- **Base de datos de componentes seleccionados:** Actualizado `wiki/peripherals/Components.md` para incluir la base de datos detallada de los 18 componentes críticos (incluyendo cristales, protecciones transitorias, conectores y semiconductores auxiliares).
- **Documentación de Tecnologías y Métodos:** Creado `wiki/core/Technologies_Methods.md` detallando las 5 tecnologías aplicadas (RMII, acondicionamiento de 0-10V, terminación split CAN, protecciones de baja capacitancia, regulación LDO) y 5 métodos empleados (diseño jerárquico complejo, rutado diferencial, control de crosstalk, keep-outs de cobre en magnetics, planos de tierra estrella GND/AGND).
- **Indexación:** Actualizado `wiki/index.md` con los nuevos recursos.

## [2026-06-10] lint | 0 issues found, 0 auto-fixed

## [2026-06-10] refactor | Organización de la carpeta raw
- **Reorganización física:** Creadas las carpetas `datasheets`, `reference_designs`, `prints`, `requirements`, `manufacturing`, `articles` dentro de `raw/` y reubicados los 12 archivos fuente para mejorar la escalabilidad y legibilidad de la base de datos.
- **Actualización en cascada (Cascade Updates):** Corregidos los enlaces en los metadatos `Raw` de los siguientes artículos:
  - [Design_Review_2026-05-18.md](core/Design_Review_2026-05-18.md)
  - [Technologies_Methods.md](core/Technologies_Methods.md)
  - [gpio-pin-configuration.md](core/gpio-pin-configuration.md)
  - [Components.md](../peripherals/Components.md)
- **Linting:** Validado de nuevo que todos los enlaces `Raw` y referencias internas apunten a ubicaciones válidas físicas.

## [2026-06-10] lint | 0 issues found, 0 auto-fixed

## [2026-06-10] ingest | Pre-Production Design Audit
- **Auditoría completa:** Creado `wiki/core/Design_Audit_2026-06-10.md` con revisión exhaustiva de 12 subsistemas del diseño de la PCB.
- **Resultado:** 5 issues críticos (🔴), 3 issues mayores (🟠), 13 issues menores (🟡). **Board NOT cleared for production.**
- **Escalación:** 4 de los 5 issues críticos ya fueron identificados en la revisión del 2026-05-18 y permanecen sin corregir en los planos del 2026-06-10.
- **Issue nuevo:** CRI-ANA-01 — Ferrite bead de 100µH en VDDA identificada como inductor incorrecto (riesgo de resonancia anti-resonante y caída de tensión excesiva).

## [2026-06-10] ingest | PDN Decoupling and Bulk Capacitance Theory
- **Artículo teórico:** Creado `wiki/core/PDN_Decoupling_Theory.md` detallando las diferencias físicas entre capacitores de bypass y bulk, cálculo de inductancia parásita de pistas, derating de MLCCs por DC bias y simulaciones matemáticas de droop.
- **Enlace:** Actualizado `wiki/index.md` para incluir el nuevo artículo.

## [2026-06-10] fix | PDN Decoupling Corrections
- **Correcciones aplicadas:** Modificados `MAJ-PDN-01` (marcado como verificado/falso positivo tras confirmar que C14 ya es de 1µF) y `MIN-PDN-02` (marcado como corregido con capacitor bulk adicional de 10µF cerca de Pin 72) en `wiki/core/Design_Audit_2026-06-10.md`.
- **Actualización de Arquitectura:** Actualizado `wiki/core/Hardware_Architecture.md` para reflejar la capacitancia de salida del LDO correcta de 1.2µF y el desacoplo de bulk distribuido, añadiendo enlaces cruzados.
- **Indexación:** Actualizado `wiki/index.md` para reflejar los recuentos correctivos y la aclaración del falso positivo en las últimas actualizaciones.

## [2026-06-10] fix | VBAT Decoupling Verification
- **Correcciones aplicadas:** Modificado `MIN-ANA-02` en `wiki/core/Design_Audit_2026-06-10.md` para marcarlo como verificado/falso positivo tras confirmar que Pin 6 (VBAT) está conectado directamente a un capacitor de desacoplo de 1µF (C50) sin ninguna resistencia de la red adyacente conectada a él.
- **Indexación:** Actualizado `wiki/index.md` para ajustar el contador de issues menores de la auditoría de 12 a 11 e indicar la verificación en el log de últimas actualizaciones.
