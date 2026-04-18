# STM32F746ZG Mapa de I/O y Restricciones - Píldora de Conocimiento
**Fuente:** STM32F746xx Datasheet (stm32f746zg.pdf), [Páginas 55-68] y UM1974.

## 1. Clasificación de Pines (I/O Structure)
- **FT (5V Tolerant):** Soportan hasta 5.5V si están configurados como entrada digital.
- **TTa (3.3V Tolerant):** Conectados directamente al ADC. **NO aplicar >3.6V**.
- **I/O Type:** La mayoría son `I/O`, algunos son `I` (solo entrada) como `BYPASS_REG`.

## 2. Mapa de Pines Críticos (NO UTILIZAR o usar con precaución)

### A. Pines de Sistema y Depuración (MANDATORIO)
| Pin STM32 | Función | Motivo de Restricción |
| :--- | :--- | :--- |
| **PA13** | JTMS-SWDIO | Datos del depurador SWD. Bloquea programación si se usa como GPIO. |
| **PA14** | JTCK-SWCLK | Reloj del depurador SWD. Bloquea programación si se usa como GPIO. |
| **PH3** | BOOT0 | Controla el modo de arranque. Debe estar a GND para ejecución normal. |
| **NRST** | Reset | Reinicia el microcontrolador. |

### B. Pines Compartidos con Periféricos de la Placa (NUCLEO)
| Pin STM32 | Compartido con... | Impacto |
| :--- | :--- | :--- |
| **PA1, PA2, PA7** | Ethernet (RMII) | Conflicto con red si se usan como GPIO. |
| **PC1, PC4, PC5** | Ethernet (RMII) | Conflicto con red si se usan como GPIO. |
| **PB11, PB12, PB13**| Ethernet (RMII) | Conflicto con red si se usan como GPIO. |
| **PD8, PD9** | ST-LINK (VCP) | UART de comunicación con el PC (consola). |
| **PC13** | Blue Button (B1) | Pulsador de usuario en la placa. |
| **PB0, PB7, PB14** | User LEDs | LEDs Verde, Azul y Rojo de la placa. |

## 3. Pines Recomendados para IO General (16 señales)
Para tu conector de 16 señales GPIO, se recomiendan pines de los puertos **PE, PF y PG** que suelen tener menos conflictos en la NUCLEO-F746ZG:

| Señal | Pin Sugerido | Tolerancia | Señal | Pin Sugerido | Tolerancia |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **IO_1** | PE2 | FT | **IO_9** | PF12 | FT |
| **IO_2** | PE4 | FT | **IO_10** | PF13 | FT |
| **IO_3** | PE5 | FT | **IO_11** | PF14 | FT |
| **IO_4** | PE6 | FT | **IO_12** | PF15 | FT |
| **IO_5** | PE11 | FT | **IO_13** | PG2 | FT |
| **IO_6** | PE12 | FT | **IO_14** | PG3 | FT |
| **IO_7** | PE14 | FT | **IO_15** | PG4 | FT |
| **IO_8** | PE15 | FT | **IO_16** | PG5 | FT |

## 4. Resumen de Tolerancia 5V
- **Pines FT:** PA8-PA12, PB5-PB15, PC6-PC12, PD0-PD15, PE0-PE15, PF0-PF15, PG0-PG15.
- **Pines TTa (MÁX 3.3V):** PA0, PA1, PA2, PA3, PA4, PA5, PA6, PA7, PB0, PB1, PC0, PC1, PC2, PC3, PC4, PC5. (Cualquier pin con función analógica ADC/DAC).
