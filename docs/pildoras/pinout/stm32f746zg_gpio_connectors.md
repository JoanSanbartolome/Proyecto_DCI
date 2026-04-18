# STM32F746ZG Conectores GPIO - Píldora de Conocimiento
**Fuente:** UM1974 (dm00244518.pdf), Capítulo 7.13 - 7.15, [Páginas 33-36, 69-72]

## 1. Arquitectura de Conectores
La placa NUCLEO-F746ZG dispone de dos sistemas de expansión principales:
- **ST Zio:** Conectores hembra (cara superior) y macho (cara inferior) que extienden Arduino Uno V3.
- **ST morpho:** Dos tiras de pines macho (CN11 y CN12) que dan acceso a **todos** los I/Os del microcontrolador.

## 2. Conectores ST Zio (GPIOs Seleccionados)
Optimizado para shields, agrupa señales por función.

| Conector | Pin | Pin STM32 | Función por Defecto | Ref. Página |
| :--- | :--- | :--- | :--- | :--- |
| **CN7** | 1 | PC6 | D16 / I2S_A_MCK | [Página 39] |
| **CN7** | 10 | PA5 | D13 / SPI_A_SCK | [Página 40] |
| **CN8** | 14 | PG2 | D49 / I/O | [Página 38] |
| **CN9** | 15 | PA7 | D71 / I/O | [Página 38] |
| **CN10** | 29 | PA0 | D32 / TIM2_CH1 | [Página 40] |
| **CN10** | 31 | PB0 | D33 / TIM3_CH3 | [Página 40] |

## 3. Conectores ST morpho (Acceso Total)
Ideal para prototipado directo y medida con analizador lógico.

### CN11 (Lado Izquierdo)
| Pin | Señal | Pin | Señal | Ref. Página |
| :--- | :--- | :--- | :--- | :--- |
| 1 | PC10 | 2 | PC11 | [Página 70] |
| 15 | PA14 (SWD) | 16 | +3.3V | [Página 70] |
| 23 | PC13 (User B1)| 24 | VIN | [Página 70] |
| 37 | PC3 | 38 | PC0 (ADC) | [Página 70] |

### CN12 (Lado Derecho)
| Pin | Señal | Pin | Señal | Ref. Página |
| :--- | :--- | :--- | :--- | :--- |
| 1 | PC9 | 2 | PC8 | [Página 70] |
| 10 | PD8 (UART TX) | 9 | GND | [Página 70] |
| 31 | PB3 | 32 | AGND | [Página 70] |
| 61 | PG14 | 62 | PF11 | [Página 70] |

## 4. Restricciones y Advertencias
- **Nivel Lógico:** Todos los I/Os son de **3.3V**. NO aplicar 5V directamente (no compatible con Arduino 5V original). [Página 37]
- **Señales Compartidas:**
  - **PA13/PA14:** Usados para el depurador SWD. No se recomienda usarlos como GPIO si el ST-LINK está activo. [Página 71]
  - **PA7:** Compartido entre el conector Zio (D11) y la interfaz Ethernet. [Página 69]
  - **PB13:** Compartido entre I2S y Ethernet (RMII_TXD1). [Página 69]
- **BOOT0:** Pin PH3 (disponible en CN11 pin 7). Por defecto a 0. [Página 71]
