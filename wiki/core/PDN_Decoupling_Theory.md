# PDN Decoupling and Bulk Capacitance Theory

> Sources: Joan San Bartolome, 2026-06-10; STMicroelectronics AN4661, 2016-02-15; STMicroelectronics AN5031, 2017-09-01
> Raw: [stm32f746zg.pdf](../../raw/datasheets/stm32f746zg.pdf); [dm00244518.pdf](../../raw/datasheets/dm00244518.pdf)

## Overview

This article outlines the mathematical and physical foundations of Power Delivery Network (PDN) design for high-performance microcontrollers, specifically focusing on the STM32F746ZG LQFP-144 package. It details the necessity of separating decoupling and bulk capacitance domains, the impact of PCB trace inductance, the physics of DC bias derating in Multilayer Ceramic Capacitors (MLCCs), and the calculation of voltage droop during simultaneous high-speed switching events.

## 1. Bypass Decoupling vs. Bulk Capacitance

The decoupling network of an integrated circuit must address noise and power delivery across a wide frequency spectrum. To do this, it is divided into two distinct functional domains:

*   **Bypass/Decoupling Capacitors (Local, 100 nF):**
    *   **Target Frequency Range:** High frequency ($1\,\text{MHz}$ to $> 100\,\text{MHz}$).
    *   **Placement:** Positioned as close as physically possible to each $V_{DD}$ pin (within $1-2\,\text{mm}$), routing directly to the ground plane.
    *   **Function:** Provide instantaneous current during the nanosecond-scale switching edges of the MCU's internal digital gates and output buffers ($t_r < 2\,\text{ns}$). Because these transient events are extremely brief, a small charge storage ($100\,\text{nF}$) is sufficient, but the path inductance (ESL and trace loop) must be minimized to avoid high impedance.
*   **Bulk Capacitors (Regional, 4.7 µF - 10 µF):**
    *   **Target Frequency Range:** Low to medium frequency (DC to $\approx 1\,\text{MHz}$).
    *   **Placement:** Placed at the entry points of the power traces into the MCU pin clusters, or distributed at opposite sides of the package.
    *   **Function:** Recharge the individual 100 nF decoupling capacitors. The local linear regulator (LDO) or Switch-Mode Power Supply (SMPS) cannot react to transient loads in the microsecond range due to its control loop bandwidth (typically $< 300\,\text{kHz}$) and the inductance of the long supply traces. The bulk capacitors bridge this temporal gap, acting as a local energy reservoir.

## 2. PCB Trace Inductance and PDN Impedance

A PCB trace behaves as a parasitic inductor with an approximate inductance of:
$$L_{trace} \approx 1\,\text{nH/mm}$$

For a large package like the LQFP-144, the physical size of the chip is approximately $20\,\text{mm} \times 20\,\text{mm}$. The distance between opposite power pins (e.g., $V_{DD\_9}$ at Pin 95 and $V_{DD\_1}$ at Pin 72) is around $25\,\text{mm}$. If only a single bulk capacitor is placed at Pin 95:
1.  The parasitic trace inductance between the capacitor and Pin 72 is $L_{trace} \approx 25\,\text{nH}$.
2.  At high frequencies, the impedance ($X_L$) of this parasitic inductance increases linearly with frequency ($X_L = 2\pi f L$).
3.  For a $50\,\text{MHz}$ clock signal (e.g., Ethernet RMII `REF_CLK`):
    $$X_L = 2\pi \cdot (50 \cdot 10^6\,\text{Hz}) \cdot (25 \cdot 10^{-9}\,\text{H}) \approx 7.85\,\Omega$$
    For high-speed digital edges, the spectral content extends to the 3rd and 5th harmonics. At $150\,\text{MHz}$, the impedance of this trace segment rises to:
    $$X_{L,150} \approx 23.5\,\Omega$$

If a high-speed peripheral switching transient occurs near Pin 72, the high impedance of the $25\,\text{nH}$ trace isolates it from the bulk capacitor at Pin 95. The local $100\,\text{nF}$ capacitors will deplete, and the local rail voltage will collapse before charge can flow from the distant bulk capacitor. Therefore, **bulk capacitance must be spatially distributed** around the package to minimize loop inductance.

## 3. MLCC DC Bias Derating

Multilayer Ceramic Capacitors (MLCCs) using Class II ferroelectric materials (such as X5R, X7R, and Y5V) exhibit a significant reduction in capacitance when a DC voltage is applied. This phenomenon, known as **DC Bias Derating**, is caused by the polarization of the barium titanate ($\text{BaTiO}_3$) crystalline structure under the electric field, which reduces its dielectric constant.

*   **4.7 µF Ceramic (0603/0805, rated at 6.3V or 10V) at 3.3V DC:**
    *   Loses between **$50\%$ and $60\%$** of its nominal capacitance.
    *   Effective In-Circuit Capacitance: **$\approx 1.88\,\mu\text{F} - 2.35\,\mu\text{F}$**.
*   **10 µF Ceramic (0805, rated at 10V or 16V) at 3.3V DC:**
    *   Loses between **$40\%$ and $50\%$** of its nominal capacitance.
    *   Effective In-Circuit Capacitance: **$\approx 5.0\,\mu\text{F} - 6.0\,\mu\text{F}$**.

To ensure that the PDN possesses an actual, physical bulk capacitance of at least $4.7\,\mu\text{F}$ to $10\,\mu\text{F}$ under operating conditions ($3.3\,\text{V}$), designers must select nominal values of **$10\,\mu\text{F}$** or higher.

## 4. Transient Current and Voltage Droop Analysis

When multiple high-speed interfaces switch simultaneously (Ethernet RMII, SPI, CAN), they charge and discharge their transmission line and receiver input capacitances ($C_{load} \approx 15\,\text{pF}$ per line).

The peak transient current ($I_{peak}$) required to charge $N$ lines with a rise time $dt$ is:
$$I_{peak} \approx N \cdot C_{load} \cdot \frac{dV}{dt}$$

For 8 RMII lines switching simultaneously at $3.3\,\text{V}$ with a rise time $dt = 2\,\text{ns}$:
$$I_{peak} \approx 8 \cdot (15 \cdot 10^{-12}\,\text{F}) \cdot \frac{3.3\,\text{V}}{2 \cdot 10^{-9}\,\text{s}} \approx 198\,\text{mA}$$

If the MCU core and internal PLLs add another $150\,\text{mA}$ of dynamic current, the total transient current step is $\Delta I \approx 150\,\text{mA}$ (sustained over a block of cycles).

During the time ($\Delta t \approx 5\,\mu\text{s}$) it takes for the LDO regulator loop to respond, the voltage droop ($\Delta V$) on the $V_{DD}$ rail is determined by the effective bulk capacitance:
$$\Delta V = \frac{I \cdot \Delta t}{C_{bulk,effective}}$$

*   **Scenario A: Single 4.7 µF nominal bulk capacitor ($C_{bulk,effective} \approx 2.0\,\mu\text{F}$):**
    $$\Delta V = \frac{0.15\,\text{A} \cdot 5\,\mu\text{s}}{2.0\,\mu\text{F}} \approx 375\,\text{mV}$$
    This drops the $3.3\,\text{V}$ rail to **$2.925\,\text{V}$**, violating the minimum operating voltage of the Ethernet PHY ($3.0\,\text{V}$) and causing intermittent clock jitter, ADC errors, or MCU resets.
*   **Scenario B: Distributed Bulk Capacitors (4.7 µF + 10 µF, $C_{bulk,effective} \approx 7.5\,\mu\text{F}$):**
    $$\Delta V = \frac{0.15\,\text{A} \cdot 5\,\mu\text{s}}{7.5\,\mu\text{F}} \approx 100\,\text{mV}$$
    This keeps the $3.3\,\text{V}$ rail at **$3.20\,\text{V}$**, which is well within the standard $5\%$ tolerance margin ($3.135\,\text{V}$ minimum).

## 5. Layout Best Practices for PDN

To minimize the parasitic inductance ($ESL_{total}$) of the decoupling path, the layout must follow these guidelines:

1.  **Vias in Pad / Close Vias:** Vias should be placed as close as possible to the capacitor pads. A standard $0.3\,\text{mm}$ via in a $1.6\,\text{mm}$ thick PCB adds $\approx 1.2\,\text{nH}$ of inductance. Connecting pads with long traces to vias defeats the purpose of high-frequency decoupling.
2.  **Dual Ground Vias:** For bulk capacitors (0805), placing two vias in parallel for the ground pad reduces the via inductance by half ($\approx 0.6\,\text{nH}$).
3.  **Plane Stack-up:** The power plane ($V_{DD}$) and ground plane ($GND$) should be on adjacent layers (e.g., Layer 2 and Layer 3 in a 4-layer stackup) separated by a thin dielectric. This creates a high-frequency interplane capacitance that helps decouple noise above $100\,\text{MHz}$.

## See Also

- [Hardware Architecture](Hardware_Architecture.md)
- [Pre-Production Design Audit](Design_Audit_2026-06-10.md)
- [Technologies & Methods](Technologies_Methods.md)
