# Via Stitching & GND Guard Rings

> Sources: Joan San Bartolome, 2026-06-13
> Raw: [via-stitching-plan-stm32f746zg.md](../../raw/articles/2026-06-13-via-stitching-plan-stm32f746zg.md); [via-guard-ring-crystal-hse-lse.md](../../raw/articles/2026-06-13-via-guard-ring-crystal-hse-lse.md)

## Overview

Este artículo define la estrategia completa de via stitching para la PCB STM32F746ZG Rev B-01 (DCI 2025/2026). El objetivo es reducir la impedancia del camino de retorno de GND entre L1 (Top) y L2 (GND plane) en el stackup de 4 capas (Signal / GND / PWR / Signal, 1.6 mm, Lab Circuits). El via stitching se aplica selectivamente bajo componentes críticos — **no** como flood stitching de la superficie completa. Incluye un sub-diseño especializado de jaulas de GND (guard rings) alrededor de los cristales HSE y LSE para apantallamiento RF de los osciladores.

---

## 1. Stackup de Referencia

```
Layer 1 (Top)   — Signal (componentes SMD, pistas)
Layer 2          — GND (plano continuo)
Layer 3          — PWR (plano P3V3)
Layer 4 (Bot)   — Signal (rutado secundario)
```

- Inductancia de via estándar (0.3 mm drill, 1.6 mm FR4): **≈ 1.2 nH**
- Capacitancia de interplano L2–L3 (GND–PWR adyacentes): activa pasivamente a frecuencias > 100 MHz si los planos están continuos y bien cosidos.

---

## 2. Geometría de Via de Cosido

| Parámetro | Valor |
|-----------|-------|
| Drill | 0.3 mm |
| Pad | **0.68 mm** ~~(0.6 mm)~~ (annular ring **0.19 mm**/lado) |
| Net | `GND` (zonas digitales) / `AGND` (zona analógica) |
| Layers | L1 (Top copper pour) → L2 (GND plane) |
| Thermal relief | Estándar en via stitching general; **desactivado** en guard rings de cristal |
| Fabricante | Lab Circuits — **Clase 5** (AR = 5.33, corona 0.19 mm) |

> [!WARNING]
> **Corrección DFM (2026-06-13):** El pad original de 0.6 mm da una corona de 0.15 mm, que incumple el mínimo de Clase 4 en capas externas (0.17 mm) e internas (0.22 mm). Pad corregido a **0.68 mm** (corona = 0.19 mm) compatible con **Clase 5** en Lab Circuits. El AR = 5.33 también supera el límite de Clase 4 (AR=5), lo que confirma la necesidad de Clase 5 (AR ≤ 6). Ver [Manufacturing Constraints](Manufacturing_Constraints.md) §3 para análisis completo.

### Doble Via en Capacitores Bulk 0805

Aplicar **2 vias en paralelo** en el pad GND de:
- **C64** (4.7 µF, VDD_9 Pin 95)
- **C_bulk adicional** (10 µF, VDD_1 Pin 72)

Separación CC entre los 2 vias: **0.65 mm**. Reducción de ESL: 1.2 nH → **0.6 nH**. No aplicar en caps 100 nF (0402): espacio insuficiente sin violar DRC.

---

## 3. Zonas de Aplicación y Espaciado

La frecuencia crítica del diseño es el 5° armónico de RMII (50 MHz):

$$f_{max} = 250\,\text{MHz} \Rightarrow \lambda_{FR4} \approx 572\,\text{mm} \Rightarrow d_{max} = \frac{\lambda}{20} \approx 28.6\,\text{mm}$$

El criterio de densidad de corriente de retorno es más restrictivo que λ/20 en las zonas críticas:

| Zona | Espaciado máximo | Justificación |
|------|-----------------|---------------|
| Cluster VDD MCU (Pins 72–95) | **1.5 mm** | Alta demanda transitoria simultánea (RMII + CAN + SPI) |
| Ethernet PHY LAN8742A (U9) | **1.5 mm** | REF_CLK 50 MHz + armónicos hasta 150 MHz |
| LDO LD39050PU33R (U4) | **2.0 mm** | Corriente de salida máx. 500 mA |
| Cristal HSE (X42) | **2.0 mm** | Guard ring; ver §4 |
| Cristal LSE (X41) | **2.0 mm** | Guard ring; ver §4 |

---

## 4. Guard Rings de Cristales Osciladores

### 4.1 Fundamento de Apantallamiento RF

La PLL del STM32F746ZG multiplica el HSE por **×27** (8 MHz → 216 MHz SYSCLK). Todo jitter de fase en la entrada se amplifica proporcionalmente. El oscilador Pierce en PH0/PH1 opera con alta impedancia de entrada, haciéndolo vulnerable a:
- Acoplamiento capacitivo de pistas digitales cercanas (RMII, SPI, GPIO)
- Corrientes de retorno que atraviesan el plano GND bajo el cristal
- Emisiones propias del cristal: 8 MHz + armónicos (16, 24, 32... MHz)

La jaula actúa como **cilindro Faraday parcial**: copper pour de GND en Top Layer + vias al plano L2.

### 4.2 Guard Ring HSE (X42 — NX3225GD-8.000M, Package 3225)

**Footprint:** 3.2 × 2.5 mm, pads 1.2 × 1.3 mm, pitch 2.3 mm.

**Copper pour:** 8.0 × 6.0 mm centrada en X42, Net = `GND`, relleno sólido, thermal relief **desactivado**.

**Distribución de 10 vias:**

| Via | Posición respecto al centro de X42 |
|-----|------------------------------------|
| V1  | (−4.0, +3.0) mm — esquina sup. izq. |
| V2  | (−2.0, +3.0) mm |
| V3  | ( 0.0, +3.0) mm — centro superior |
| V4  | (+2.0, +3.0) mm |
| V5  | (+4.0, +3.0) mm — esquina sup. der. |
| V6  | (−4.0, −3.0) mm — esquina inf. izq. |
| V7  | (−2.0, −3.0) mm |
| V8  | ( 0.0, −3.0) mm — centro inferior |
| V9  | (+2.0, −3.0) mm |
| V10 | (+4.0, −3.0) mm — esquina inf. der. |

Opcionales para cierre lateral: V11 (−4.0, 0.0) y V12 (+4.0, 0.0).

**Reglas internas a la jaula:**

| Parámetro | Valor |
|-----------|-------|
| Longitud pistas PH0/PH1 | ≤ 5 mm (ideal < 3 mm) |
| Ancho pistas PH0/PH1 | 0.2 mm |
| Vias en PH0/PH1 | ❌ Prohibido |
| Separación PH0↔PH1 | ≥ 0.3 mm (regla 3W) |
| Clearance copper-to-pad X42 | ≥ 0.5 mm |
| Clearance copper-to-pad C45/C47 | ≥ 0.4 mm |

**Impacto esperado:**

| Métrica | Sin jaula | Con jaula |
|---------|-----------|-----------|
| Impedancia GND a 8 MHz | ~2–5 Ω | < 0.1 Ω |
| Rechazo acoplamiento externo | 0 dB | −20 a −30 dB |
| Jitter inducido por ruido | 15–50 ps RMS | < 5 ps RMS |

### 4.3 Guard Ring LSE (X41 — NX3215SA, Package 3215)

| Parámetro | Valor |
|-----------|-------|
| Copper pour | 7.0 × 4.0 mm centrada en X41 |
| Vias | 8 (4 en borde superior + 4 en borde inferior) |
| Espaciado entre vias | 2.0 mm |
| Geometría via | Igual que HSE: 0.3/0.6 mm, Direct Connect, Net = `GND` |

El LSE es menos crítico en RF (32.768 kHz) pero sensible a ruido de baja frecuencia (ripple LDO, 50/60 Hz). La jaula proporciona pantalla de campo eléctrico.

---

## 5. Gestión de la Frontera AGND / GND

> **Regla absoluta:** Los vias de cosido GND y AGND no cruzan jamás la frontera entre dominios.

- **Dominio GND digital:** Net = `GND`, copper pour conectada a L2 GND plane.
- **Dominio AGND analógico:** Net = `AGND`, copper pour independiente conectada al plano AGND de L2.
- **Punto estrella:** Unión única en un bridge de L2 junto a los pines VSS/VSSA del MCU. No añadir vias adicionales que cortocircuiten los dominios.
- **Keepout en la frontera:** Región de exclusión de 0.5 mm de ancho a lo largo de la frontera AGND/GND donde no se colocan vias de stitching.

---

## 6. Reglas DRC en Altium

| Regla | Categoría | Valor |
|-------|-----------|-------|
| `ViaStitch_Clearance` | Electrical → Clearance | 0.65 mm (via pad a pad de señal) |
| `ViaStitch_MinSpacing` | Electrical → Clearance | 0.35 mm (via pad a via pad) |
| `AGND_GND_NoMix` | Electrical → Short-Circuit | Violación si GND y AGND coexisten a < 0.5 mm |
| `Crystal_GND_NoThermal` | Plane → Polygon Connect Style | Direct Connect en InComponent('X42') OR InComponent('X41') OR InComponent('C45') OR InComponent('C47') OR InComponent('C41') OR InComponent('C42') |

---

## 7. Zonas de Exclusión

| Zona | Restricción |
|------|-------------|
| Bajo RJ45/magnéticos (CN14) | Keepout absoluto de todo cobre (ya definido) |
| Pares diferenciales USB D+/D- | Clearance ≥ 0.65 mm desde via pad |
| Pares MDI Ethernet TX±/RX± | Clearance ≥ 0.65 mm desde via pad |
| Frontera AGND/GND | Keepout de 0.5 mm — sin vias de ningún tipo |

---

## 8. Procedimiento de Implementación (Altium)

1. **Verificar copper pours:** Net `GND` en zona digital, `AGND` en zona analógica. Rebuild All Polygon Pours.
2. **Doble via en C64 y C_bulk:** Colocar manualmente 2 vias paralelos a 0.65 mm CC en pad GND de cada bulk cap.
3. **Via stitching automático por zona:** `Place → Via Stitching/Shielding → Add Stitching to Net`. Grid según zona (1.5 / 2.0 / 2.5 mm).
4. **Guard ring HSE:** Colocar 10 vias manualmente en coordenadas de §4.2. Dibujar Polygon Pour 8×6 mm. Aplicar regla `Crystal_GND_NoThermal`.
5. **Guard ring LSE:** Misma secuencia con parámetros de §4.3.
6. **DRC:** Cero violaciones en zonas de cristal y frontera AGND/GND.
7. **Verificación 3D:** Confirmar jaulas visualmente; sin pistas penetrando en ellas.

---

## See Also

- [Manufacturing Constraints](Manufacturing_Constraints.md) — **DFM Analysis:** via pad correction, fabrication class, annular ring limits
- [Hardware Architecture](Hardware_Architecture.md)
- [PDN Decoupling Theory](PDN_Decoupling_Theory.md)
- [Pre-Production Audit 2026-06-10](Design_Audit_2026-06-10.md)
- [Technologies & Methods](Technologies_Methods.md)
- [Ethernet Interface](../peripherals/ethernet-interface.md)
- [Analog Conditioning](../peripherals/analog-conditioning.md)
