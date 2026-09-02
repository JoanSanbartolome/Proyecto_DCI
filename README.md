# Proyecto de Diseño de PCB - STM32F746ZG

![MCU](https://img.shields.io/badge/MCU-STM32F746ZG-003545?style=for-the-badge&logo=stmicroelectronics&logoColor=white)
![EDA](https://img.shields.io/badge/EDA-Altium%20Designer-A200FF?style=for-the-badge&logo=altiumdesigner&logoColor=white)
![Informe](https://img.shields.io/badge/Informe-LaTeX-008080?style=for-the-badge&logo=latex&logoColor=white)
![Wiki](https://img.shields.io/badge/Wiki-Markdown-000000?style=for-the-badge&logo=markdown&logoColor=white)

Este repositorio contiene el proyecto completo de diseño de circuito impreso (PCB) para una placa basada en el microcontrolador **STM32F746ZG**, abarcando esquemáticos, trazado de PCB (layout), archivos de fabricación, informe de diseño y una wiki técnica detallada.

---

## 📂 Estructura del Repositorio

```text
├── scr/            # Fuentes del proyecto en Altium Designer (.SchDoc, .PcbDoc, .PrjPCB, Librerías)
├── fabricacion/    # Archivos de producción (Gerber, BOM, Pick & Place, Stackup, PDF de esquemáticos)
├── informe/        # Código fuente LaTeX e imágenes del informe de diseño
├── Informe_Placa.pdf # Informe técnico final compilado en PDF
├── wiki/           # Base de conocimiento y documentación técnica estructurada
└── outputs/        # Documentos de análisis e investigación técnica intermedia
```

---

## ⚡ Especificaciones del Sistema

* **Microcontrolador:** STM32F746ZG (ARM Cortex-M7 con FPU)
* **Interfaces Periféricas:**
  * **Ethernet:** Interfaz RMII con PHY Ethernet y conector MagJack.
  * **USB:** USB OTG High-Speed / Full-Speed con pares diferenciales adaptados a 90 Ω.
  * **CAN Bus:** Transceptor CAN con protección ESD, filtro de modo común y terminación dividida (120 Ω).
  * **Memoria Flash SPI:** Memoria externa Quad-SPI con control de integridad de señal.
  * **Acondicionamiento Analógico:** Entradas ADC filtradas con amplificadores operacionales en buffer.

---

## 🛠️ Herramientas Utilizadas

* **CAD / EDA:** Altium Designer
* **Documentación Técnica:** LaTeX / Markdown
* **Control de Versiones:** Git & GitHub

---

## 📚 Wiki Técnica (`wiki/`)

La carpeta `wiki/` sirve como memoria de diseño e investigación técnica organizada en:

* **`core/`**: Arquitectura hardware, requerimientos del proyecto, reglas de layout/vias y restricciones de fabricación.
* **`peripherals/`**: Guías de diseño para Ethernet, CAN, USB, SPI y acondicionado analógico.
* **`protection/`**: Estrategias de protección ESD y sobretensión por periférico.
* **`theory/`**: Teoría de integridad de señal, desacoplo de la red PDN y guías de diseño PCB.
