# NUCLEO-F746ZG - Píldora de Conocimiento (Hardware)
**Fuente:** UM1974 (dm00244518.pdf), Capítulos 7.13 - 7.14, [Páginas 33-40]

## 1. Resumen de Hardware
- **Placa:** NUCLEO-144 con microcontrolador STM32F746ZG.
- **Depurador:** ST-LINK/V2-1 integrado (conector USB Micro-B).
- **Alimentación:**
  - USB ST-LINK (500mA limit).
  - VIN (7V-12V) en CN8 pin 15 o CN11 pin 24.
  - E5V (5V externa) en CN11 pin 6.
  - +3.3V directa en CN8 pin 7 o CN11 pin 16.

## 2. Conectores de Expansión (ST Zio)
Los conectores CN7, CN8, CN9 y CN10 proporcionan compatibilidad con Arduino Uno V3 y acceso a GPIOs adicionales.

### Pines Críticos para ADC (Señales Analógicas)
| Pin Zio | Nombre | Pin STM32 | Función ADC | Ref. Página |
| :--- | :--- | :--- | :--- | :--- |
| CN9-1 | A0 | PA3 | ADC123_IN3 | [Página 38] |
| CN9-3 | A1 | PC0 | ADC123_IN10 | [Página 38] |
| CN9-5 | A2 | PC3 | ADC123_IN13 | [Página 38] |
| CN9-7 | A3 | PF3 | ADC3_IN9 | [Página 38] |
| CN10-7 | A6 | PB1 | ADC12_IN9 | [Página 40] |
| CN10-9 | A7 | PC2 | ADC123_IN12 | [Página 40] |

### Comunicaciones (Jerarquía)
- **USART (Virtual COM Port):** PD8 (TX) y PD9 (RX) conectados al ST-LINK por defecto.
- **Ethernet:** RMII interface usando PA1, PA2, PA7, PB11, PB12, PB13, PC1, PC4, PC5.
- **USB OTG:** PA8 (SOF), PA9 (VBUS), PA10 (ID), PA11 (DM), PA12 (DP).

## 3. Configuración de Puentes (Solder Bridges) Críticos
| Bridge | Estado Defecto | Función | Ref. Página |
| :--- | :--- | :--- | :--- |
| SB12 | OFF | Conecta VREF+ a VDDA. Quitar si se inyecta VREF externa en CN7-6. | [Página 37] |
| SB13 | ON | Conecta VDD_LINK a VDD_MCU (alimentación ST-LINK). | [Página 21] |

## 4. LEDs y Pulsadores
- **LD1 (COM):** LED tricolor (comunicación ST-LINK).
- **LD2 (User):** Azul (PB7).
- **LD3 (User):** Rojo (PB14).
- **LD4 (User):** Verde (PB0).
- **B1 (User):** Pulsador azul (PC13).
- **B2 (Reset):** Pulsador negro (NRST). [Página 25]
