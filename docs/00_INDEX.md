# Índice Maestro de Documentación (DCI)

Este índice sirve como mapa para localizar la información técnica necesaria para el proyecto STM32F746ZG.

## Documentos Principales

| Archivo | Tipo de Documento | Descripción / Uso Crítico |
| :--- | :--- | :--- |
| `dm00244518.pdf` | **Reference Manual (RM0385)** | Mapas de registros detallados, secuencias de inicialización de periféricos y arquitectura del sistema. |
| `stm32f746zg.pdf` | **Datasheet (DS10649)** | Límites eléctricos, características de consumo, encapsulados y tablas de *Alternate Functions* de los pines. |
| `nucleo-f746zg.pdf` | **User Manual (UM1974)** | Esquemas de la placa de desarrollo, configuración de jumpers y conectores ST Zio/morpho. |
| `Propuesta+Diseño+DCI+25-26.pdf` | **Diseño de Proyecto** | Requisitos técnicos, objetivos y arquitectura específica de la práctica. |

## Píldoras de Conocimiento Generadas

### 1. Periféricos
- [Alimentación y Distribución](pildoras/perifericos/alimentacion_y_distribucion.md): Detalle de reguladores (LDO), buses de 5V/3.3V y dominios analógicos.

### 2. Pinout y Hardware
- [NUCLEO-F746ZG Hardware](pildoras/pinout/nucleo_f746zg_hardware.md): Conectores Zio, alimentación, puentes y mapeo de pines.
- [STM32F746ZG Conectores GPIO](pildoras/pinout/stm32f746zg_gpio_connectors.md): Detalle de conectores Zio y morpho, señales compartidas y restricciones de 3.3V.
- [NUCLEO-F746ZG Jumpers y Reloj](pildoras/pinout/mapa_jumpers_y_reloj.md): Catálogo de puentes para eliminación y análisis del sistema de reloj externo (HSE/LSE).

### 3. Core y Sistema
*(Aún no hay píldoras)*
