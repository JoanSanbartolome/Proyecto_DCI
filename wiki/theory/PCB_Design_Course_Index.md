# PCB Design Course Index: Theory & Best Practices

> Sources: Sergio Lopez-Buedo, UAM (MUISE), 2025/2026; STMicroelectronics; Texas Instruments; Intel
> Raw: [raw/teory/](../../raw/teory/)

## Overview

This index structures the theoretical and practical knowledge from the PCB Design course materials added to the `raw/teory/` directory. It organizes the 25 reference documents into 5 thematic blocks to assist in design reviews, signal integrity simulations, stack-up definitions, and electromagnetic compatibility (EMC) compliance.

---

## 🗺️ Thematic Blocks

### Block 1: Design Flow & Altium Designer
Reference materials for schematic capture, advanced footprints, libraries, and layout routing workflow.

*   **1a - Vista general y flujo de diseño:** [PDF](../../raw/teory/1a_-_Vista_general_y_flujo_de_dise_o.pdf)
    *   *Topics:* Conception, schematics, routing, manufacturing output (Gerbers/ODB++), assembly.
*   **1b - Tutorial Altium Designer - Version 20.2:** [PDF](../../raw/teory/1b_-_Tutorial_Altium_Designer_-_Version__20.2.pdf)
    *   *Topics:* Altium interface, project creation, schematic libraries, PCB rules, routing.
*   **2a - Altium - Esquemas avanzados:** [PDF](../../raw/teory/2a_-_Altium_-_Esquemas_avanzados.pdf)
    *   *Topics:* Hierarchical schematics, multi-channel design, net classes, bus routing.
*   **3a - Altium - Librerías:** [PDF](../../raw/teory/3a_-_Altium_-_Librer_as.pdf)
    *   *Topics:* Schematic library symbols, PCB footprint footprints, 3D body integration, IPC-compliant footprints.
*   **4-5-6 Altium - Diseño de Layout:** [PDF](../../raw/teory/4-5-6_Altium_-_Dise_o_de_Layout.pdf)
    *   *Topics:* Placement strategy, routing engines, length matching, design rule checks (DRC).

### Block 2: Signal Integrity & Transmission Lines
Physics of high-speed signals, return currents, impedance matching, reflections, and crosstalk.

*   **4a - Integridad de Señal - Metodología de Diseño:** [PDF](../../raw/teory/4a_-_Integridad_de_Se_al_-_Metodolog_a_de_Dise_o.pdf)
    *   *Topics:* Rise time ($t_r$), bandwidth, lumped vs. distributed circuits, high-speed threshold ($l > \frac{v \cdot t_r}{6}$).
*   **5a - Caminos de retorno - Pistas en un PCB - Crosstalk:** [PDF](../../raw/teory/5a_-_Caminos_de_retorno_-_Pistas_en_un_PCB_-_Crosstalk.pdf)
    *   *Topics:* Return current paths (minimum impedance path), microstrip vs. stripline geometry, capacitive/inductive crosstalk, spacing rules ($3W$ rule).
*   **5b - Reflexiones y Terminaciones:** [PDF](../../raw/teory/5b_-_Reflexiones_y_Terminaciones.pdf)
    *   *Topics:* Transmission lines, characteristic impedance ($Z_0$), reflection coefficient ($\Gamma$), termination topologies (series, parallel, AC, differential).
*   **7 - Líneas diferenciales:** [PDF](../../raw/teory/7_-_L_neas_diferenciales.pdf)
    *   *Topics:* Differential signals, odd/even mode impedance, differential impedance ($Z_{diff}$), routing rules (symmetric, no vias, length matching).
*   **TI - AN-806 Data Transmission Lines:** [PDF](../../raw/teory/TI_-_AN-806_Data_Transmission_Lines_and_Their_Characteristics_-_snla026a.pdf)
    *   *Topics:* Classic transmission line theory, coaxial cables, twisted pairs, microstrip math.
*   **TI - AN-807 Reflections Computations:** [PDF](../../raw/teory/TI_-_AN-807_Reflections_Computations_and_Waveforms_-_snla027b.pdf)
    *   *Topics:* Graphical reflection analysis, lattice diagrams, overshoot/undershoot equations.

### Block 3: Stack-up Design & High-Speed Constraints
Multi-layer PCB planning, dielectric selection, interplane capacitance, and constraint rules.

*   **6a - Diseño de Stack-Ups:** [PDF](../../raw/teory/6a_-_Dise_o_de_Stack-Ups.pdf)
    *   *Topics:* Layer allocation, power/ground plane pairing, symmetric stack-ups, standard thickness.
*   **6b - Altium Diseño Stack-Ups y reglas High-Speed:** [PDF](../../raw/teory/6b_-_Altium_Dise_o_Stack-Ups_y_reglas_High-Speed.pdf)
    *   *Topics:* Altium Layer Stack Manager, impedance profiles, high-speed constraint rules.
*   **6c - Altium Simulaciones SI:** [PDF](../../raw/teory/6c_-_Altium_Simulaciones_SI.pdf)
    *   *Topics:* Running Signal Integrity simulations in Altium, reflection analysis, crosstalk analysis.
*   **ALTERA-INTEL High-Speed Board Layout Guidelines:** [PDF](../../raw/teory/ALTERA-INTEL_High-Speed_Board_Layout_Guidelines_-_AN224.pdf)
    *   *Topics:* High-speed FPGA board layout, decoupling, differential signals, clock routing.
*   **Altium Designer Module 22-Signal Integrity:** [PDF](../../raw/teory/Altium_Designer_Module_22-Signal_Integrity.pdf)
    *   *Topics:* Configuration and analysis tools for Altium SI simulator.

### Block 4: Power Distribution Network (PDN) & EMC
Decoupling capacitor selection, power plane designs, and electromagnetic compatibility.

*   **8 - Redes de desacoplo:** [PDF](../../raw/teory/8_-_Redes_de_desacoplo.pdf)
    *   *Topics:* PDN impedance target ($Z_{target}$), bulk/bypass capacitor analysis, loop inductance, capacitor mounting.
*   **Recomendaciones_potencia:** [PDF](../../raw/teory/Recomendaciones_potencia.pdf)
    *   *Topics:* Detailed decoupling strategies, low-impedance plane designs, LDO/SMPS decoupling.
*   **Libro_EMC:** [PDF](../../raw/teory/Libro_EMC.pdf)
    *   *Topics:* Comprehensive guide to electromagnetic compatibility, emissions, susceptibility, shielding, filtering.
*   **TI High-Speed Layout Guidelines:** [PDF](../../raw/teory/TI_High-Speed_Layout_Guidelines_-_SCAA082A.pdf)
    *   *Topics:* Power distribution, ground planes, bypass capacitors, routing high-speed clocks.

### Block 5: Manufacturing, DFM & Best Practices
PCB fabrication processes, design-for-manufacturing limitations, and schematic readability.

*   **3b - Buenas prácticas en esquemas:** [PDF](../../raw/teory/3b_-_Buenas_pr_cticas_en_esquemas.pdf)
    *   *Topics:* Logical layout, clear labeling, power symbols, off-sheet connectors.
*   **3c - Limitaciones Fabricación PCBs y DFM:** [PDF](../../raw/teory/3c_-_Limitaciones_Fabricaci_n_PCBs_y_DFM.pdf)
    *   *Topics:* Minimum trace width/spacing, annular ring size, drill size limits, copper clearances, solder mask clearance.
*   **3d - Fabricación PCBs:** [PDF](../../raw/teory/3d_-_Fabricaci_n_PCBs.pdf)
    *   *Topics:* Imaging, etching, plating, drilling, solder mask, surface finishes (ENIG, HASL).
*   **DisenyoCircuitosImpresos-34628_2025:** [PDF](../../raw/teory/DisenyoCircuitosImpresos-34628_2025.pdf)
    *   *Topics:* Syllabus, course guidelines, grading.

---

## See Also

*   [index](../index.md)
*   [Hardware Architecture](../core/Hardware_Architecture.md)
*   [PDN Decoupling Theory](../core/PDN_Decoupling_Theory.md)
*   [Design Technologies & Methods](../core/Technologies_Methods.md)
