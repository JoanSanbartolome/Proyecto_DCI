# NUCLEO-F746ZG Alimentación y Distribución - Píldora de Conocimiento
**Fuente:** UM1974 (dm00244518.pdf), Capítulo 7.4, [Páginas 21-25]

## 1. Arquitectura de Potencia (Power Tree)
La placa gestiona tres niveles de tensión principales mediante una estructura jerárquica:

### A. Línea de 5V (Bus Principal)
Es el origen de todas las demás tensiones. En la placa original se selecciona mediante **JP3**:
- **U5V:** Proveniente del USB ST-LINK (limitado por hardware a 500mA).
- **E5V:** Entrada externa de 5V directos (CN11 pin 6).
- **VIN:** Entrada de 7V-12V (CN8 pin 15 / CN11 pin 24) regulada internamente a 5V.

### B. Línea de 3.3V (VDD)
Generada a partir de los 5V mediante el regulador lineal **U6** (LD39050PU33R).
- **Capacidad:** 500mA máximo.
- **Uso:** Alimenta el Core del STM32, I/Os, y el chip Ethernet PHY.
- **Punto de Medida:** Jumper **JP5 (IDD)**, permite medir el consumo exclusivo del microcontrolador.

### C. Dominios Especiales
- **VDDA / VREF+:** Alimentación analógica para el ADC. Conectada a VDD por defecto mediante **SB12**.
- **VBAT:** Alimentación de backup (RTC y registros). Conectada a VDD por defecto mediante **SB156**.

## 2. Componentes Integrados (CIs) Clave
| Componente | Función Técnica | Impacto en Diseño Fijo | Ref. Página |
| :--- | :--- | :--- | :--- |
| **U4 (ST890)** | Power Switch inteligente. Limita corriente USB a 500mA y reporta fallos (LD5). | **Eliminar** si no usas USB para alimentar. | [Página 22] |
| **U6 (LD39050)** | LDO Regulator 5V -> 3.3V. Corazón de la alimentación del micro. | **Mantener** o equivalente (500mA min). | [Página 23] |
| **LD6 (PWR)** | LED verde indicador de presencia de 3.3V. | **Mantener** para depuración visual. | [Página 25] |

## 3. Secuencia de Alimentación Crítica
Cuando se alimenta por **VIN** o **E5V** y se usa el ST-LINK simultáneamente:
1. Conectar primero la fuente externa (**VIN/E5V**).
2. Verificar que **LD6** encienda.
3. Conectar el cable USB al PC.
*Nota: Si se invierte el orden, el PC puede limitar la corriente a 100mA y el micro no arrancará correctamente.* [Página 24]

## 4. Recomendaciones para Diseño Personalizado (Sin Jumpers)
1. **Unificar VDD/VDDA:** Conectar directamente a la salida del LDO de 3.3V.
2. **Filtrado ADC:** Colocar una perla de ferrita entre VDD y VDDA para reducir ruido de conmutación digital en las medidas del ADC.
3. **Desacoplo:** Insertar condensadores de 100nF lo más cerca posible de **cada** pin VDD del STM32.
