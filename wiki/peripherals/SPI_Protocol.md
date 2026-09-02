# SPI Protocol Implementation

> Sources: Joan San Bartolome, 2026-04-22; Altium Resources Blog (Z. Peterson), 2026-02-17
> Raw: [2026-06-13-altium-spi-trace-impedance-signal-integrity.md](../../raw/articles/2026-06-13-altium-spi-trace-impedance-signal-integrity.md); [Articulo_altium_SPI_SI.md](../../raw/articles/Articulo_altium_SPI_SI.md)

## Overview

Guía de implementación práctica del bus SPI en la PCB STM32F746ZG Rev B-01. Cubre la asignación de pines (SPI2, Port B), las restricciones de frecuencia, el dimensionado de resistencias de terminación, y las reglas de layout. La teoría de integridad de señal que fundamenta estas decisiones se desarrolla en profundidad en [SPI Signal Integrity Theory](../theory/SPI_Signal_Integrity_Theory.md).

---

## 1. Frecuencias Máximas y Restricciones

| Peripheral | Frecuencia máxima | Nota |
|-----------|-------------------|------|
| SPI1, SPI4, SPI5, SPI6 | 54 MHz | APB2 clock |
| SPI2, SPI3 | **27 MHz** | APB1 clock — usado en este diseño |
| SST25VF040B Flash | 50 MHz (Mode 0 y 3) | El periférico admite más que el bus |
| **SPI1 en PA5** | **40 MHz** (limitado) | PA5 comparte función con User LED |

> [!IMPORTANT]
> Usar SPI2 (Port B) para el Flash SPI. Máximo operativo: **27 MHz**. El SST25VF040B soporta hasta 50 MHz, por lo que el cuello de botella es el APB1 del STM32.

---

## 2. Asignación de Pines — SPI2 (Port B, LQFP-144)

Todos los pines del SPI2 agrupados en el lado derecho del package LQFP-144, minimizando el cruce de rutas:

| Señal | Pin MCU | Número de Pin | Función AF |
|-------|---------|---------------|-----------|
| SCK | PB10 | 69 | AF5 (SPI2_SCK) |
| CS (NSS) | PB12 | 73 | GPIO Output (software NSS) |
| MISO | PB14 | 75 | AF5 (SPI2_MISO) |
| MOSI | PB15 | 76 | AF5 (SPI2_MOSI) |

**Pines a evitar:**
- **PB13 (Pin 74):** reservado para RMII_TXD1 (Ethernet). Adyacente a PB14 (MISO) — riesgo de crosstalk documentado en [Design Audit MIN-SPI-02](../core/Design_Audit_2026-06-10.md).
- **PA7:** reservado para RMII_CRS_DV.
- **PA5:** penalización de frecuencia + comparte función con User LED.

---

## 3. Terminaciones Serie — Dimensionado y Colocación

### 3.1 Justificación (¿Por Qué Poner Resistencias?)

El bus SPI2 de este diseño es **eléctricamente corto** (trazas < 30 mm, t_r ≈ 2–4 ns → límite corto ≈ 43–57 mm). Las resistencias serie **no hacen impedance matching**; su función es:

1. **Amortiguar el ringing** del modelo LC del bus (inductancia de traza + capacitancia de carga).
2. **Frenar el flanco** para reducir el contenido espectral de alta frecuencia → menos crosstalk y menos EMI.
3. **Proteger contra latch-up** si hay sobreimpulsos en la carga.

### 3.2 Valores Recomendados

| Señal | Valor serie | Ubicación | Estado |
|-------|------------|-----------|--------|
| SCK (PB10) | **22–33 Ω** (0402) | En pad del MCU | ✅ Implementado |
| MOSI (PB15) | **33 Ω** (0402) | En pad del MCU | ⚠️ MIN-SPI-01: falta añadir |
| MISO (PB14) | No recomendada | — | No necesaria (señal de entrada al MCU) |
| CS (PB12) | No recomendada | — | Señal lenta; no crítica |

> [!WARNING]
> **MIN-SPI-01 (🟡 MINOR — Design Audit):** Falta resistencia serie en MOSI (PB15). Añadir **33 Ω 0402** en la salida PB15, colocado a ≤ 1 mm del pad del MCU. Sin esta resistencia hay riesgo de ringing en MOSI que puede provocar errores de escritura en el Flash en condiciones de temperatura extrema o EMI elevada.

### 3.3 Cálculo de Verificación de Setup/Hold

Antes de fijar el valor final de R_s, verificar que el flanco resultante no viola el timing del SST25VF040B:

$$t_{r,new} = 2.2 \times (R_{driver} + R_s) \times C_{load}$$

Con R_driver = 30 Ω, R_s = 33 Ω, C_load = 25 pF (estimación con traza de 20 mm):

$$t_{r,new} = 2.2 \times 63 \times 25 \times 10^{-12} \approx 3.5 \text{ ns}$$

El SST25VF040B requiere t_r < periodo de SCK / 4 = 1 / (4 × 27 MHz) ≈ **9.3 ns** → ✅ cumple con margen.

---

## 4. Configuración de GPIO

| Parámetro | Valor | Motivo |
|-----------|-------|--------|
| OSPEEDR (SCK, MOSI, MISO) | `11` = Very High Speed | Garantiza t_r < 4 ns a la frecuencia de operación |
| PUPDR (MISO) | Pull-up débil (10 kΩ) | Estado definido cuando Flash no conduce |
| OTYPER | Push-Pull | Standard para salidas SPI |
| AF | AF5 para PB10/14/15 | Alternate Function 5 = SPI2 en Port B |

---

## 5. Hardware de Soporte

| Componente | Valor | Función |
|-----------|-------|---------|
| Pull-up en CS (PB12) | **10 kΩ a 3.3V** | Estado definido al inicio (Flash inactivo = CS high) |
| WP# (Write Protect) del Flash | Tie a 3.3V si no se usa | Deshabilita escritura por hardware; obligatorio para evitar corrupción |
| HOLD# del Flash | Tie a 3.3V si no se usa | Evita pausa involuntaria del bus |
| Desacoplo en VCC del Flash | 100 nF + 1 µF (junto al pin) | Noise en VCC causa errores de lectura/escritura |

---

## 6. Reglas de Layout PCB

### 6.1 Reglas Críticas (SCK)

| Regla | Valor | Motivo |
|-------|-------|--------|
| Plano de referencia bajo SCK | GND sólido (L2) | Retorno de corriente de mínima inductancia |
| Vias en SCK | **Cero vias** | Cada via añade ~1.2 nH → ringing adicional |
| Longitud máxima SCK | < 40 mm | Criterio de bus corto con margen |
| Ancho de traza SCK | ≥ 0.15 mm (outer layer) | Reduce impedancia de traza y su inductancia |

### 6.2 Separación y Crosstalk (MIN-SPI-02)

> [!CAUTION]
> **MIN-SPI-02 (Design Audit):** PB13 (RMII_TXD1) es adyacente a PB14 (MISO) en el LQFP-144. Las trazas RMII y SPI pueden acoplarse capacitivamente si se enrutan en la misma capa y en paralelo.

Mitigación:
- Separar PB13 y PB14 en **capas distintas** (L1 y L4) para el tramo en que son paralelas.
- Si están en la misma capa: separación ≥ **3× ancho de traza** (regla 3W).
- Insertar un via de GND (stitching) entre ambas pistas si la separación es inferior a 3W.

### 6.3 Tabla de Reglas de Layout Completa

| Regla | Valor |
|-------|-------|
| R_serie SCK | ≤ 1 mm del pad MCU PB10 |
| R_serie MOSI | ≤ 1 mm del pad MCU PB15 |
| Via GND junto a R_serie | ≤ 0.5 mm del pad GND de la resistencia |
| Length matching SCK / MOSI / MISO | Tolerancia **10–15 mm** |
| Separación SCK ↔ MISO | ≥ 3× ancho traza (regla 3W) |
| Separación PB13 (RMII) ↔ PB14 (MISO) | Capas distintas o ≥ 3W en misma capa |
| CS routing | Puede bordear otras trazas (baja frecuencia) |
| Copper pour GND junto a trazas SPI | Recomendado si no hay plano continuo cerca |

---

## 7. Issues Pendientes del Design Audit

| ID | Severidad | Descripción | Acción |
|----|-----------|-------------|--------|
| MIN-SPI-01 | 🟡 MINOR | Sin resistencia serie en MOSI (PB15-SI) | Añadir **33 Ω 0402** en PB15 |
| MIN-SPI-02 | 🟡 MINOR | PB13 (RMII) adyacente a PB14 (MISO) → crosstalk | Separación de capas + GND guard via |

---

## See Also

- [SPI Signal Integrity Theory](../theory/SPI_Signal_Integrity_Theory.md) — Teoría completa: longitud eléctrica, modelos LC, terminaciones
- [Design Audit 2026-06-10](../core/Design_Audit_2026-06-10.md) — MIN-SPI-01, MIN-SPI-02
- [Technologies & Methods](../core/Technologies_Methods.md) — Método 3W, rutado diferencial
- [Ethernet Interface](ethernet-interface.md) — RMII en PB13: fuente del crosstalk
- [Component Selection](Components.md) — SST25VF040B Flash datasheet reference
