# NUCLEO-F746ZG Mapa de Jumpers y Reloj - Píldora de Conocimiento
**Fuente:** UM1974 (dm00244518.pdf), Capítulos 7.4 - 7.12, [Páginas 21-32]

## 1. Mapa de Jumpers para PCB Personalizada
Este catálogo clasifica los jumpers de la placa original y define la acción para tu diseño fijo (sin ST-LINK y sin shields).

### A. Alimentación y Sistema (CRÍTICOS - Conexión Directa)
| Referencia | Función Original | Acción en tu PCB |
| :--- | :--- | :--- |
| **JP5 (IDD)** | Puente para medir consumo del MCU. | **Cerrar (Pista)** |
| **SB156** | Conecta VBAT a VDD. | **Cerrar (Pista)** |
| **SB12** | Conecta VDDA/VREF+ a VDD. | **Cerrar (Pista)** |
| **SB2** | Conecta +3.3V_PER a +3.3V. | **Cerrar (Pista)** |

### B. Reloj y Osciladores (Configuración Fija)
| Referencia | Función Original | Acción en tu PCB |
| :--- | :--- | :--- |
| **SB112 / SB149** | Trae reloj MCO desde ST-LINK. | **ELIMINAR / Abrir** |
| **SB8 / SB9** | Conecta cristal X3 (HSE). | **Cerrar (Conectar Cristal)** |
| **SB148 / SB163** | Selección de modo HSE. | **Fijar según diseño** |

### C. Comunicaciones (Eliminar Modularidad)
| Referencia | Función Original | Acción en tu PCB |
| :--- | :--- | :--- |
| **CN4** | Jumpers del ST-LINK (SWD). | **ELIMINAR** (Conectar directo a SWD) |
| **SB5 / SB6** | UART hacia ST-LINK (VCP). | **ELIMINAR** |
| **SB13 / SB160** | Señales Ethernet RMII. | **Cerrar (Conectar a PHY)** |
| **SB132 / SB133** | Señales USB (DP/DM). | **Cerrar (Conectar a USB)** |

---

## 2. Análisis del Sistema de Reloj (Clock)
Al eliminar el ST-LINK, pierdes la fuente de reloj por defecto (MCO de 8MHz). Debes implementar una fuente propia.

### A. Reloj Principal (HSE - High Speed External)
- **Necesidad:** Obligatorio para Ethernet (estabilidad de frecuencia).
- **Configuración:** Cristal de **8 MHz** conectado a los pines **PF0-OSC_IN** y **PF1-OSC_OUT**.
- **Componentes:**
  - Cristal: 8 MHz (ej. NX3225GD).
  - Condensadores: 2x 4.3 pF a 10 pF (ajustar según capacitancia de carga del cristal).

### B. Reloj RMII (Ethernet)
- **Crítico:** La interfaz RMII requiere un reloj de **50 MHz**.
- **Estrategia sugerida:**
  1. Usa un chip PHY (ej. LAN8742A) con su propio cristal de 25 MHz.
  2. Configura el PHY para generar los 50 MHz y envíalos al pin **PA1 (RMII_REF_CLK)** del STM32.

### C. Reloj de Tiempo Real (LSE - Low Speed External)
- **Frecuencia:** 32.768 kHz.
- **Conexión:** Pines **PC14** y **PC15**.
- **Uso:** Necesario si vas a usar el RTC (calendario/reloj interno).

---

## 3. Documentación del Conector SWD (100 mils)
Para programar y depurar sin el ST-LINK integrado, implementa este pinout en tu tira de pines de 2.54mm:

| Pin | Señal | Descripción |
| :--- | :--- | :--- |
| 1 | VCC (3.3V) | Referencia de voltaje para el programador. |
| 2 | SWCLK | Pin PA14 del STM32. |
| 3 | GND | Masa común. |
| 4 | SWDIO | Pin PA13 del STM32. |
| 5 | NRST | Reset del micro (opcional pero recomendado). |
| 6 | SWO | Pin PB3 (opcional para trazado de datos). |
