# USB Design Guidelines

> Sources: STM32F746ZG Datasheet; Nucleo-144 User Manual; High-Speed Digital Design (Johnson & Graham)
> Raw: [2026-05-14-usb-impedance-power.md](../../raw/peripherals/2026-05-14-usb-impedance-power.md)

## Overview
Implementation guidelines for USB 2.0 Full Speed (12 Mbps) and High Speed (480 Mbps) connectivity on the STM32F746ZG, focusing on impedance matching, power delivery, high-frequency via modeling, and signal integrity constraints.

## Differential Impedance & Routing
* **Target:** $90 \Omega \pm 10\%$ differential impedance ($Z_{diff}$).
* **Matching:** The STM32F746ZG integrated transceiver includes internal matching. No external series resistors are required on D+/D- lines for FS operation. For HS (with external ULPI PHY), consult the specific PHY datasheet.
* **Trace Symmetry:** Keep $D+$ and $D-$ traces strictly symmetrical. Ensure length matching to within $\pm 0.15\text{ mm}$ (approx. $6\text{ mils}$) to minimize phase skew and prevent common-mode noise conversion.
* **Reference Plane:** Route the differential pair over a single, solid ground (GND) plane. Do not cross plane splits (e.g., analog/digital GND cuts or power boundaries).

---

## High-Frequency Via Modeling and Design
Adding vias to a high-speed differential pair introduces parasitic capacitance and inductance, causing impedance discontinuities, signal reflections, and degradation of the eye diagram. 

### 1. Via Parasitic Approximations
A standard plated through-hole (PTH) via can be modeled as a lumped $\pi$-equivalent circuit of parasitic capacitance ($C_{via}$) and inductance ($L_{via}$).

#### Parasitic Capacitance ($C_{via}$)
The capacitance of a via passing through a reference plane is governed by the dielectric constant, the thickness of the board, and the clearance (antipad) around the via:
$$C_{via} = \frac{1.41 \cdot \varepsilon_r \cdot T \cdot D_{pad}}{D_{antipad} - D_{pad}} \quad \text{[pF]}$$
Where:
* $\varepsilon_r$: Relative permittivity of the substrate (e.g., $4.2 - 4.5$ for FR-4).
* $T$: Substrate thickness (or thickness of the dielectric between reference planes) in inches.
* $D_{pad}$: Diameter of the via pad in inches.
* $D_{antipad}$: Diameter of the clearance hole (antipad) in the reference plane in inches.

#### Parasitic Inductance ($L_{via}$)
The self-inductance of the via barrel is primarily a function of its physical length and drill diameter:
$$L_{via} \approx 5.08 \cdot h \cdot \left[ \ln\left( \frac{4h}{d} \right) + 1 \right] \quad \text{[nH]}$$
Where:
* $h$: Via barrel length (typically total board thickness) in inches.
* $d$: Via drill diameter in inches.

#### Via Characteristic Impedance ($Z_{via}$)
The characteristic impedance of a single-ended via can be approximated by:
$$Z_{via} = \sqrt{\frac{L_{via}}{C_{via}}} \quad [\Omega]$$
In typical PCBs, standard vias without optimization yield an impedance of $35 \Omega$ to $40 \Omega$. This severe capacitive dip causes significant reflections.

### 2. Via Optimization Rules for USB Differential Pairs
To match the $90 \Omega$ differential target ($Z_{diff} \approx 2 \times Z_{via} \cdot (1 - k)$ where $k$ is the coupling coefficient):
* **Minimize Via Count:** Ideally, route $D+/D-$ entirely on a single layer (preferably the top layer) without vias. If vias are unavoidable, limit the count to **maximum 2 per trace**.
* **Increase Antipad Diameter ($D_{antipad}$):** Enlarge the ground plane clearance surrounding the via pad. This reduces $C_{via}$, pulling the impedance back up toward $50 \Omega$ (single-ended) / $90 \Omega$ (differential). A typical layout uses a $0.2\text{ mm}$ drill, $0.45\text{ mm}$ pad, and $0.9 - 1.0\text{ mm}$ antipad.
* **GND Return Vias (Stitching Vias):** When transition between layers occurs, the return current must also change layers. Place a ground return via **immediately adjacent** (distance $< 1\text{ mm}$) to each signal via. This provides a low-impedance continuous return path, minimizing the loop inductance loop and EMI.
* **Symmetrical Via Placement:** Place the vias for $D+$ and $D-$ as a tightly coupled pair with identical spacing as the trace separation to preserve differential coupling and phase symmetry.
* **Remove Non-Functional Pads:** Delete unused pads on inner layers of the via to minimize capacitive loading.
* **Avoid Via Stubs:** If routing terminates on an internal layer, the unused portion of the via acts as an open circuit stub (resonant line). For USB 2.0 High Speed (480 Mbps), backdrill or use blind/buried vias if board thickness and budget permit, or keep stubs under $0.5\text{ mm}$.

---

## Signal Isolation & Reference Planes (Crosstalk Constraints)
Differential pairs are robust against external common-mode noise, but they can still couple noise to adjacent traces or pick up high-frequency interference.

### 1. What Signals Can Run Over or Under the USB Pair?
* **With a Solid GND Plane:** If a solid, uninterrupted ground plane exists between the USB layer and an adjacent signal layer, **any digital or analog signal** can cross under/over the pair. The ground plane acts as an electrostatic shield, reducing crosstalk to negligible levels.
* **Without a Reference Plane (Adjacent Layers in Direct Contact):** 
  * **Absolutely Prohibited:** No high-speed digital lines (HSE/LSE crystals, SDRAM signals, SPI, I2C, PWM) or switching power supply nodes (buck/boost SW pins, gate drivers) should run parallel to or cross the differential pair.
  * **Critical Violations:** Do not run power lines ($VBUS$, $+5\text{V}$, $+3.3\text{V}$) parallel to $D+/D-$ without adequate spacing.
  * **If Crossing is Inevitable:** If a low-frequency control signal (e.g., static enable pin) must cross the differential pair, it **must cross strictly at $90^\circ$** to minimize the mutual coupling area.

### 2. Spacing Rules (The "3W" Rule)
To prevent lateral crosstalk on the same layer:
* Maintain a clearance of at least $3 \times W$ (where $W$ is the trace width of a single USB trace) between the outer edge of the USB pair and any neighboring non-USB copper trace.
* For critical switching nodes (e.g., DC-DC switch node) or RF/clock signals, increase this clearance to $5 \times W$ or place a ground guard trace between them.

---

## Power Delivery
* **VDDUSB:** Dedicated supply for the USB transceiver. Must be $3.0\text{V}$ to $3.6\text{V}$ and bypassed locally with a $100\text{nF}$ ceramic capacitor placed immediately at the pin.
* **VBUS Input:** If using USB to power the board, connect VBUS to the $5\text{V}$ regulator input through a Schottky diode (e.g., BAT60) to prevent back-powering the host.
* **Sensing:** Connect VBUS to PA9 (VBUS sensing) through a resistor divider to protect the GPIO ($10\text{k}\Omega$ series or a divider if non-5V tolerant, though PA9 on STM32F746ZG is $5\text{V}$-tolerant when powered at $V_{DD} > 2.0\text{V}$).

---

## ESD and EMI Protection
* **ESD Suppressor Placement:** Place a specialized ESD protection chip like the **USBLC6-4SC6** as close as possible to the physical USB connector. The signal lines must flow **directly through** the ESD pads to avoid creating stubs.
* **Common Mode Choke (Optional but Recommended):** Place a common-mode choke (CMC) close to the connector (upstream of the ESD protector) to filter out high-frequency electromagnetic emissions and common-mode noise.

---
*Document status: Curated knowledge base.*
*Source Citation: [STM32F746xx Datasheet DS10922 Rev 4, Section 3.36]*
