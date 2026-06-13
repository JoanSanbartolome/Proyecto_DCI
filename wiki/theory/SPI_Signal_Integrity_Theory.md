# SPI Signal Integrity — Teoría de Líneas de Transmisión

> Sources: Altium Resources Blog (Zachariah Peterson), 2026-02-17
> Raw: [2026-06-13-altium-spi-trace-impedance-signal-integrity.md](../../raw/articles/2026-06-13-altium-spi-trace-impedance-signal-integrity.md); [Articulo_altium_SPI_SI.md](../../raw/articles/Articulo_altium_SPI_SI.md)

## Overview

El bus SPI no tiene un requisito de impedancia de traza especificado en el estándar. Sin embargo, los fenómenos de integridad de señal — ringing, reflexiones, crosstalk — sí pueden aparecer, y su severidad depende de si el bus es eléctricamente "largo" o "corto" respecto al tiempo de subida de la señal. Este artículo sintetiza la teoría completa: criterio de longitud eléctrica, modelos de circuito equivalente, estrategias de terminación y guías de layout, con aplicación directa al diseño STM32F746ZG + SST25VF040B (SPI2, hasta 27 MHz).

---

## 1. El Estándar SPI y la Impedancia de Traza

El estándar SPI **no especifica ninguna impedancia característica** para las trazas. Cualquier guía que afirme "SPI necesita 50 Ω" está tomando una convención de conveniencia, no un requisito del estándar. Las guías que sí citan un rango lo hacen típicamente entre 30 Ω y 150 Ω — un rango tan amplio que no es útil.

La realidad: **la impedancia solo importa cuando el bus es eléctricamente largo**, condición que depende del tiempo de subida de la señal, no de la frecuencia de datos.

> El uso de 50 Ω como target en SPI obedece a que es el target de otras señales en la misma placa, lo que simplifica la fabricación (único target para el stackup).

---

## 2. Criterio de Longitud Eléctrica

### 2.1 Definición

Un bus es "eléctricamente corto" cuando su retardo de propagación es negligible respecto al tiempo de subida de la señal. El criterio conservador (10%):

$$\text{Bus corto si: } T_{prop} < 0.1 \times t_r$$

donde $T_{prop}$ es el tiempo de propagación de extremo a extremo de la traza.

### 2.2 Velocidad de Propagación en FR-4

$$v_{prop} = \frac{c}{\sqrt{D_k}} = \frac{3 \times 10^8}{\sqrt{4.4}} \approx 1.43 \times 10^8 \text{ m/s} \approx 143 \text{ mm/ns}$$

Para el stackup de este diseño (FR-4, $D_k \approx 4.4$):

$$T_{prop} \approx \frac{L_{traza} \text{ [mm]}}{143 \text{ mm/ns}}$$

### 2.3 Cálculo del Límite de Longitud para este Diseño

Para el SPI2 del STM32F746ZG con el SST25VF040B a 27 MHz:
- El tiempo de subida del STM32 en modo "Very High Speed" es **t_r ≈ 2–4 ns** (aprox. a 50 pF de carga).
- Con t_r = 3 ns (valor central):

$$L_{max, 10\%} = 0.1 \times 3 \text{ ns} \times 143 \text{ mm/ns} \approx 43 \text{ mm}$$

Con t_r = 4 ns:

$$L_{max, 10\%} \approx 57 \text{ mm}$$

> [!IMPORTANT]
> Para el SPI2 de este diseño (trazas en placa, < 30 mm), el bus es **eléctricamente corto** en todos los escenarios. La terminación serie (22–33 Ω en SCK) no es estrictamente necesaria para impedance matching, pero sí es útil para **amortiguar ringing y reducir EMI** — que es la razón correcta para colocarla.

### 2.4 Comparación con Otros Criterios

| Criterio | Límite de longitud (t_r = 3 ns, FR-4) |
|----------|--------------------------------------|
| 10% (conservador, recomendado) | 43 mm |
| 20% | 86 mm |
| 50% (permisivo) | 214 mm |

La mayoría de rutas SPI en placa caen por debajo del criterio del 10%, especialmente con GPIOs de alta velocidad.

---

## 3. Determinación del Tiempo de Subida

El tiempo de subida en el bus está determinado principalmente por:

$$t_r \approx 2.2 \times R_{driver} \times C_{load}$$

donde:
- $R_{driver}$: impedancia de salida del GPIO del STM32 en modo "Very High Speed" ≈ **25–40 Ω**
- $C_{load}$: capacitancia total del bus = $C_{pad\_flash} + C_{traza}$
  - $C_{pad\_flash}$ (SST25VF040B): especificado en datasheet (típicamente 5–15 pF)
  - $C_{traza}$ ≈ longitud [mm] × capacitancia por mm de la línea (≈ 0.1 pF/mm para 0402 outer layer en FR-4)

Para trazas de 20 mm y C_load = 20 pF:

$$t_r \approx 2.2 \times 30 \times 20 \times 10^{-12} = 1.32 \text{ ns}$$

Esto sitúa el t_r en el rango de 1–4 ns para condiciones típicas de este diseño.

---

## 4. Modelo de Circuito Equivalente

### 4.1 Bus Corto — Modelo Lumped LC

Cuando el bus es corto, la línea no se comporta como línea de transmisión sino como un circuito concentrado:

```
Driver             Traza                Flash
 ┌───┐    R_s      ┌──────────────┐     ┌───┐
 │MCU├──/\/\/─────┤ L_traza      ├─────┤    │
 └───┘             │ C_traza      │     │ C  │
                   └──────┬───────┘     └───┘
                          │ GND
```

- **L_traza** ≈ 1 nH/mm (inductancia de traza sobre plano GND)
- **C_traza** ≈ 0.1 pF/mm (plano GND próximo)
- **C_load**: capacitancia de entrada del Flash (datasheet SST25VF040B)
- Si L es elevada y t_r es corto → oscilación subamortiguada (ringing)

### 4.2 Efecto del Resistor Serie

Añadir $R_s$ en el driver:
- **Amortiguación**: la resistencia aumenta el factor de amortiguación $\zeta$ del circuito LC → reduce el ringing.
- **Ralentización del flanco**: $t_r$ aumenta según $t_{r,new} \approx 2.2 \times (R_{driver} + R_s) \times C_{load}$.
- **No es matching**: en un bus corto, $R_s$ no actúa como terminación de impedancia sino como elemento de amortiguación.

> [!WARNING]
> **No sobrefrenar el flanco.** Si $R_s$ es demasiado grande, el tiempo de subida resultante puede violar los tiempos de setup/hold de la interfaz. Verificar siempre con la fórmula $t_{r,new}$ contra los requisitos de timing del SST25VF040B.

### 4.3 Bus Largo — Modelo de Línea de Transmisión

En el caso (infrecuente) de un bus largo, la línea debe tratarse como una línea de transmisión con $Z_0$ característico. La terminación serie:

$$R_s = Z_0 - R_{driver}$$

Para $Z_0 = 50 \Omega$ y $R_{driver} = 30 \Omega$: $R_s = 20 \Omega$ → valor estándar: **22 Ω**.

Efecto en tensiones:
- En el driver: $V_{driver} = V_{logic} \times \frac{Z_0}{R_{driver} + R_s + Z_0}$ durante el tránsito.
- En el receptor: el doble por reflexión en la impedancia capacitiva de entrada → la tensión final es $V_{logic}$ (correcto).

---

## 5. Crosstalk y Emisiones EMI

El contenido espectral de la señal SPI se extiende hasta frecuencias mucho mayores que la frecuencia de datos:

$$f_{-3dB} \approx \frac{0.35}{t_r}$$

Para t_r = 3 ns: $f_{-3dB} \approx 117 \text{ MHz}$.

El crosstalk capacitivo entre líneas SPI adyacentes (especialmente SCK hacia MOSI/MISO) aumenta linealmente con la frecuencia. Las emisiones de modo común también aumentan. Por ello:

- **Frenar el flanco** (con $R_s$) reduce directamente el espectro de alta frecuencia → menos crosstalk y menos EMI.
- **Separación de trazas**: aplicar la regla 3W (separación ≥ 3× ancho de traza) entre SCK y MISO para este diseño (crosstalk documentado en [SPI Protocol](SPI_Protocol.md) y [Design Audit](../core/Design_Audit_2026-06-10.md) MIN-SPI-02).

---

## 6. Relación con el Diseño STM32F746ZG + SST25VF040B

| Parámetro | Valor en este diseño |
|-----------|---------------------|
| Interface | SPI2 en Port B (PB10, PB12, PB14, PB15) |
| Frecuencia máxima SPI2 | 27 MHz (APB1/2 limitado) |
| t_r estimado (GPIO OSPEEDR=11) | 2–4 ns @ 50 pF |
| Longitud traza estimada | < 30 mm (en placa) |
| Criterio λ/10% | Bus corto a 43–57 mm → **bus corto** |
| Terminación serie SCK | 22–33 Ω: función **amortiguación**, no matching |
| Terminación MOSI | No implementada (MIN-SPI-01 del audit: recomienda 33 Ω en PB15) |
| Crosstalk PB13 (RMII) → PB14 (MISO) | Riesgo documentado en audit (MIN-SPI-02) |

> [!NOTE]
> La terminación de 22–33 Ω en SCK está justificada por amortiguación de ringing y reducción de crosstalk, no por matching de impedancia. El bus SPI2 es eléctricamente corto para todas las condiciones operativas de este diseño.

---

## 7. Guías de Layout — Síntesis Completa

| Regla | Valor / Criterio | Justificación |
|-------|-----------------|---------------|
| Plano de referencia | GND sólido bajo todas las trazas SPI | Retorno de corriente, reducción inductancia de loop |
| Ancho de traza (outer layer) | 2–2.5× distancia al plano GND | Reduce inductancia y capacitancia parásita unitaria |
| 3W entre SCK y MISO | Separación ≥ 3× ancho traza | Crosstalk capacitivo entre líneas |
| Vias en SCK | Evitar (cero vias ideal) | Cada via añade ~1.2 nH de inductancia serie |
| Resistencia serie SCK | 22–33 Ω en driver | Amortiguación ringing + reducción EMI |
| Resistencia serie MOSI | 33 Ω en driver (MIN-SPI-01) | Idem |
| GND via junto a resistencia serie | ≤ 0.5 mm del pad GND | Minimizar inductancia de retorno local |
| Length matching SCK/MOSI/MISO | Tolerancia 10–15 mm | Skew temporal entre señales |
| CS routing | Menos crítico | CS no lleva datos a alta frecuencia |
| Separación de RMII/SPI | > 3W o capa diferente | Riesgo de coupling PB13→PB14 (MIN-SPI-02) |

---

## See Also

- [SPI Protocol](SPI_Protocol.md) — Implementación práctica: pinout, frecuencias, hardware SPI2
- [Design Audit 2026-06-10](../core/Design_Audit_2026-06-10.md) — MIN-SPI-01, MIN-SPI-02
- [Technologies & Methods](../core/Technologies_Methods.md) — Método de rutado diferencial y regla 3W
- [Via Stitching & GND Guard Rings](../core/Via_Stitching_GND_Guard_Rings.md) — PDN y retorno de corriente
- [PCB Design Course Index](PCB_Design_Course_Index.md) — Bloque 2 (SI), Bloque 3 (Stack-ups y reflexiones)
