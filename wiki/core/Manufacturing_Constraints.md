# Manufacturing Constraints — Lab Circuits DFM

> Sources: Lab Circuits S.A., 2023-04-28
> Raw: [2026-06-13-lab-circuits-parametros-fabricacion.md](../../raw/manufacturing/2026-06-13-lab-circuits-parametros-fabricacion.md)

## Overview

Este artículo compila y analiza los parámetros de fabricación de **Lab Circuits S.A.** aplicados al diseño de la PCB STM32F746ZG Rev B-01. El fabricante ofrece 5 clases de precisión (3 a 7) con restricciones de mínimos crecientes en taladros, trazas, coronas y aislamientos. Se determina la clase aplicable al diseño actual, se listan las restricciones DFM vinculantes, y se identifican los componentes críticos que están en riesgo de violar dichos límites.

---

## 1. Determinación de la Clase de Fabricación

### 1.1 Parámetros del Diseño

| Parámetro de Diseño | Valor |
|---------------------|-------|
| Número de capas | 4 (Signal / GND / PWR / Signal) |
| Espesor total PCB | 1.6 mm |
| Via stitching drill | 0.3 mm |
| Via stitching pad | 0.6 mm (annular ring = 0.15 mm/lado) |
| Grosor de cobre en capas externas | 35 µm (1 oz, estándar) |
| Grosor de cobre en capas internas | 17–35 µm (½–1 oz, planos de cobre) |
| Microvías HDI | ❌ No — solo vias pasantes estándar |

### 1.2 Clase de Trabajo: **Clase 6** (confirmado)

La clase de fabricación ha sido confirmada como **Clase 6** para este proyecto. Con Clase 6:
- Drill mínimo metalizado: **0.20 mm** → el drill de 0.3 mm tiene margen del 50%.
- AR máximo: **8** → con PCB de 1.6 mm y drill 0.3 mm, AR = 5.33 cumple con margen.
- Capacidades HDI disponibles (microvías 0.075 mm) — **no usadas** en este diseño.

> [!IMPORTANT]
> **Clase 6 en Lab Circuits es la clase de fabricación de este proyecto.** Con esta clase, todos los parámetros del via stitching (0.3 mm drill) cumplen sin conflictos. Las capacidades mínimas de traza (0.150 mm en externas, 0.125 mm en internas para cobre de 35 µm) son más permisivas que Clase 4/5 y permiten rutar trazas impedance-controlled sin problemas.

### 1.3 Aspect Ratio — Verificación

El Aspect Ratio (AR) de un taladro pasante en PCB de 1.6 mm de grosor:

$$AR = \frac{\text{grosor PCB}}{\text{diámetro drill}} = \frac{1.6 \text{ mm}}{0.3 \text{ mm}} = 5.33$$

- **Clase 6 máximo AR: 8** → ✅ AR = 5.33 cumple con **margen del 33%**
- Clase 5 máximo AR: 6 → ✅ También cumple (dato de referencia)
- Clase 4 máximo AR: 5 → ❌ Excedido (dato histórico, no aplica)

> [!NOTE]
> El AR = 5.33 es cómodo para Clase 6 (límite = 8). No hay conflicto.

---

## 2. Restricciones DFM Vinculantes — Clase 6

### 2.1 Taladros

| Parámetro | Límite Clase 6 | Valor en Diseño | Estado |
|-----------|---------------|-----------------|--------|
| Drill mínimo metalizado | **0.20 mm** | 0.30 mm (via stitching) | ✅ 50% margen |
| Drill mínimo NO metalizado | **0.30 mm** | 3.5 mm (mounting holes) | ✅ |
| Aspect Ratio máximo | **8** | 5.33 (1.6mm/0.3mm) | ✅ Margen del 33% |

### 2.2 Trazas en Capas Externas (cobre 35 µm / 1 oz)

| Parámetro | Límite Clase 6 | Aplicación en Diseño | Estado |
|-----------|---------------|---------------------|--------|
| Ancho/espacio mínimo traza | **0.150 mm** | Trazas señal general: ≥ 0.2 mm | ✅ Margen 33% |
| Pares diferenciales (100 Ω Ethernet, 90 Ω USB) | 0.150 mm | Anchos calc. por stackup (orientativo: ~0.19–0.22 mm) | ✅ |

> [!NOTE]
> Con Clase 6, el mínimo de traza en capas externas es **0.150 mm** para cobre de 35 µm. Los anchos de traza impedance-controlled para Ethernet y USB (orientativamente 0.19–0.22 mm) cumplen holgadamente. Usar el Layer Stack Manager de Altium con los parámetros exactos del stackup de Lab Circuits para el cálculo final.

#### Cálculo orientativo de ancho de traza para 50 Ω (microstrip, cobre 35 µm, FR4 estándar 1.6 mm)

Para el stackup 4 capas (L1-Signal / L2-GND / L3-PWR / L4-Signal):
- Dieléctrico típico L1→L2: ≈ 0.36 mm (FR4 prepreg estándar)
- Ancho de traza para 50 Ω ≈ **0.63 mm** (microstrip exterior) → bien por encima del mínimo de 0.150 mm
- Ancho de traza para 100 Ω diferencial ≈ 2 × 0.19 mm con gap 0.2 mm → **dentro de límites Clase 6**
- Ancho de traza para 90 Ω diferencial (USB) ≈ 2 × 0.22 mm con gap 0.18 mm → **dentro de límites Clase 6**

### 2.3 Trazas en Capas Internas (cobre 35 µm / 1 oz, planos)

| Parámetro | Límite Clase 6 | Diseño | Estado |
|-----------|---------------|--------|--------|
| Ancho/espacio mínimo (35 µm) | **0.125 mm** | Rutado interno mínimo ≥ 0.15 mm | ✅ Margen |
| Ancho/espacio mínimo (17 µm) | 0.100 mm | Planos GND/PWR | ✅ |

### 2.4 Corona Mínima (Annular Ring)

La corona es la diferencia entre el radio del pad y el radio del drill:

$$\text{Corona} = \frac{D_{pad} - D_{drill}}{2}$$

Para el via de stitching con pad original (0.3 mm drill / 0.6 mm pad):

$$\text{Corona}_{orig} = \frac{0.6 - 0.3}{2} = 0.15 \text{ mm}$$

Para el via de stitching con pad recomendado (0.3 mm drill / **0.68 mm** pad):

$$\text{Corona}_{rec} = \frac{0.68 - 0.3}{2} = 0.19 \text{ mm}$$

| Capa | Límite Clase 6 | Corona pad 0.6 mm | Corona pad 0.68 mm | Estado |
|------|---------------|-------------------|-------------------|--------|
| Capas externas | **0.10 mm** | 0.15 mm | 0.19 mm | ✅ Ambos cumplen |
| Capas internas señal | **0.15 mm** | **0.15 mm (= límite)** | **0.19 mm** | ⚠️ Pad 0.6 mm está en el límite exacto — recomendado 0.68 mm para margen |

> [!NOTE]
> Con **Clase 6** no hay conflicto de corona. El pad original de 0.6 mm cumple el mínimo (corona = 0.15 mm = límite interno exacto). Sin embargo, se mantiene el **pad de 0.68 mm** (corona = 0.19 mm) como valor recomendado para tener **27% de margen** sobre el límite interno y evitar rechazos por tolerancias de taladrado (±0.05 mm en drill).

### 2.5 Aislamiento entre Taladro Metalizado y Conductor

| Límite Clase 6 | Valor Diseño | Estado |
|---------------|-------------|--------|
| **0.25 mm** | 0.65 mm clearance via-a-señal | ✅ Margen del 160% |

El clearance de 0.65 mm cumple holgadamente el requisito de Clase 6.

---

## 3. Verificación DFM Completa — Clase 6

✅ **No existen conflictos DFM en Clase 6.** Todos los parámetros del diseño cumplen los requisitos con margen.

| # | Parámetro | Valor Diseño | Límite Clase 6 | Estado | Margen |
|---|---------|-------------|---------------|--------|---------|
| 1 | Aspect Ratio | 5.33 | ≤ 8 | ✅ | 33% |
| 2 | Drill via stitching | 0.3 mm | ≥ 0.20 mm | ✅ | 50% |
| 3 | Corona externas (pad 0.68 mm) | 0.19 mm | ≥ 0.10 mm | ✅ | 90% |
| 4 | Corona internas (pad 0.68 mm) | 0.19 mm | ≥ 0.15 mm | ✅ | 27% |
| 5 | Corona internas (pad 0.60 mm) | 0.15 mm | ≥ 0.15 mm | ⚠️ En límite | 0% |
| 6 | Aislamiento taladro-conductor | 0.65 mm | ≥ 0.25 mm | ✅ | 160% |
| 7 | Traza mínima ext. (35 µm) | 0.2 mm general | ≥ 0.150 mm | ✅ | 33% |
| 8 | Traza mínima int. (35 µm) | 0.15 mm mínimo | ≥ 0.125 mm | ✅ | 20% |

> [!IMPORTANT]
> **Conclusión DFM Clase 6:** El diseño cumple **todos** los requisitos de Lab Circuits Clase 6. La única situación a vigilar es la corona interna con el pad original de 0.6 mm (corona = 0.15 mm = límite exacto, margen cero). Se recomienda mantener el **pad en 0.68 mm** para absorber la tolerancia de taladrado (±0.05 mm de Lab Circuits).


---

## 4. Parámetros Verificados — Sin Problemas

| Parámetro | Valor Diseño | Límite Clase 6 | Estado |
|-----------|-------------|----------------|--------|
| Drill mounting holes (3.5 mm) | 3.5 mm NO metalizado | ≥ 0.30 mm | ✅ Amplio margen |
| Clearance via-traza | 0.65 mm | ≥ 0.25 mm | ✅ |
| Ancho trazas señal general (≥ 0.2 mm) | ≥ 0.2 mm | 0.150 mm (ext., 35 µm) | ✅ Margen 33% |
| Ancho trazas diff (Ethernet/USB) | 0.19–0.22 mm | 0.150 mm | ✅ |
| Microvías HDI | No usadas | 0.075 mm disponible | ✅ (No aplica) |
| Resin filling vias | No requerido | Disponible | ✅ (No aplica) |

---

## 5. Tabla de Capacidades Completa de Lab Circuits — Referencia

### Taladros

| Parámetro | Clase 3 | Clase 4 | Clase 5 | Clase 6 | Clase 7 |
|-----------|:-------:|:-------:|:-------:|:-------:|:-------:|
| Drill metalizado mínimo | 0.50 mm | 0.30 mm | 0.30 mm | 0.20 mm | 0.15 mm |
| Drill NO metalizado mínimo | 0.60 mm | 0.40 mm | 0.40 mm | 0.30 mm | 0.25 mm |
| Aspect Ratio máximo | 5 | 5 | 6 | 8 | 13 (PCB ≤ 2 mm) |

### Trazas — Capas Externas (por grosor cobre base)

| Grosor cobre | Clase 3 | Clase 4 | Clase 5 | Clase 6 | Clase 7 |
|-------------|:-------:|:-------:|:-------:|:-------:|:-------:|
| 17 µm (½ oz) | 0.300 | 0.200 | 0.150 | 0.125 | 0.100 mm |
| 35 µm (1 oz) | 0.300 | 0.200 | 0.150 | 0.150 | — |
| 70 µm (2 oz) | 0.350 | 0.250 | 0.200 | 0.175 | — |
| 105 µm (3 oz) | 0.350 | 0.300 | 0.250 | 0.200 | — |

### Trazas — Capas Internas (por grosor cobre base)

| Grosor cobre | Clase 3 | Clase 4 | Clase 5 | Clase 6 | Clase 7 |
|-------------|:-------:|:-------:|:-------:|:-------:|:-------:|
| 17 µm (½ oz) | 0.250 | 0.150 | 0.125 | 0.100 | 0.075 mm |
| 35 µm (1 oz) | 0.300 | 0.200 | 0.150 | 0.125 | 0.100 mm |
| 70 µm (2 oz) | 0.300 | 0.200 | 0.175 | 0.150 | — |
| 105 µm (3 oz) | 0.350 | 0.300 | 0.250 | 0.200 | — |

### Corona Mínima (Annular Ring)

| Capa | Clase 3 | Clase 4 | Clase 5 | Clase 6 | Clase 7 |
|------|:-------:|:-------:|:-------:|:-------:|:-------:|
| Externas | 0.22 mm | 0.17 mm | 0.13 mm | 0.10 mm | 0.075 mm |
| Internas (señal) | 0.25 mm | 0.22 mm | 0.19 mm | 0.15 mm | 0.125 mm |

### Aislamiento entre Taladro Metalizado y Conductor

| Clase 3 | Clase 4 | Clase 5 | Clase 6 | Clase 7 |
|:-------:|:-------:|:-------:|:-------:|:-------:|
| 0.40 mm | 0.40 mm | 0.30 mm | 0.25 mm | 0.20 mm |

### Microvías HDI

| Parámetro | Clase 5 | Clase 6 | Clase 7 |
|-----------|:-------:|:-------:|:-------:|
| Diámetro mínimo microvía | 0.10 mm | 0.075 mm | 0.075 mm |
| Grosor aislante máximo | 0.10 mm | 0.065 mm | 0.065 mm |
| Pad superior | 0.35 mm | 0.30 mm | 0.25 mm |
| Pad inferior | 0.30 mm | 0.25 mm | 0.25 mm |
| Pared mínima microvía ↔ taladro pasante | 0.22 mm | 0.15 mm | 0.10 mm |
| Pared mínima entre microvías | 0.23 mm | 0.15 mm | 0.10 mm |
| Pared mínima entre microvías 2 niveles | 0.23 mm | 0.15 mm | 0.10 mm |

---

## 6. Impacto sobre el Via Stitching — Estado con Clase 6

El artículo [Via Stitching & GND Guard Rings](Via_Stitching_GND_Guard_Rings.md) usa vias de **0.3 mm drill / 0.68 mm pad**. Con Clase 6, este pad es correcto y tiene buen margen:

| Parámetro | Valor en Diseño | Límite Clase 6 | Estado | Margen |
|-----------|----------------|---------------|--------|---------|
| Drill | 0.3 mm | ≥ 0.20 mm | ✅ | 50% |
| Pad | 0.68 mm | N/A (solo drill y corona) | ✅ | — |
| Corona externas | 0.19 mm | ≥ 0.10 mm | ✅ | 90% |
| Corona internas | 0.19 mm | ≥ 0.15 mm | ✅ | 27% |
| Clase de fabricación | — | **Clase 6** | ✅ confirmado | — |

> [!NOTE]
> El pad original de 0.6 mm también cumple Clase 6 (corona int. = 0.15 mm = límite). Se mantiene el pad de **0.68 mm** para absorber la tolerancia de taladrado de Lab Circuits (±0.05 mm).

---

## 7. Reglas DRC para Altium — Clase 6

| Regla | Valor | Motivo |
|-------|-------|--------|
| Via drill (stitching) | **0.3 mm** | Compatible con Clase 6 (mín. 0.20 mm) |
| Via pad (stitching) | **0.68 mm** (recomendado) | Corona internas = 0.19 mm (27% margen sobre mínimo 0.15 mm) |
| Clase de fabricación en Gerber | **Clase 6** | Confirmado por proyecto |
| Minimum Annular Ring (outer) | **0.10 mm** (límite Clase 6) | DRC check rule |
| Minimum Annular Ring (inner) | **0.15 mm** (límite Clase 6) | DRC check rule |
| Hole-to-conductor clearance | **0.65 mm** | ≥ 0.25 mm (Clase 6) — sin cambio |
| Traza mínima ext. (35 µm) | **0.150 mm** | Límite Clase 6 |
| Traza mínima int. (35 µm) | **0.125 mm** | Límite Clase 6 |

---

## See Also

- [Via Stitching & GND Guard Rings](Via_Stitching_GND_Guard_Rings.md) — Via geometry affected by DFM constraints
- [Hardware Architecture](Hardware_Architecture.md) — PCB stackup: 4 layers, 1.6 mm, Lab Circuits
- [Design Audit 2026-06-10](Design_Audit_2026-06-10.md) — Pre-production audit reference
- [PCB Design Course Index](../theory/PCB_Design_Course_Index.md) — Block 5: DFM & Manufacturing
