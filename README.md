# Proyecto DCI - STM32F746ZG

Este repositorio contiene el desarrollo del proyecto para la placa **NUCLEO-F746ZG**. 

## 📂 Gestión Documental Inteligente

Para manejar eficientemente los manuales técnicos de STMicroelectronics (RM0385, Datasheets, User Manuals) sin sufrir fatiga de contexto ni alucinaciones, este proyecto utiliza un **Sistema de Conocimiento Intermedio y Extracción Quirúrgica**.

### 🛠️ Componentes del Sistema

1.  **Índice Maestro (`docs/00_INDEX.md`)**: Mapa de referencia que vincula cada PDF con su propósito técnico y capítulos críticos.
2.  **Píldoras de Conocimiento (`docs/pildoras/`)**: Archivos Markdown técnicos, breves y precisos, extraídos de los manuales. Contienen solo registros, bits y secuencias de inicialización.
3.  **Agente Extractor**: Un protocolo de sub-agente especializado que sigue el **Manual de Operaciones (`docs/instrucciones_agente_extractor.md`)** para garantizar fidelidad técnica y concisión.

### 🔄 Flujo de Trabajo con Documentación

1.  **Localizar**: Identificar el documento y el rango de páginas en el Índice Maestro o mediante búsqueda externa.
2.  **Extraer**: Invocar al Agente Extractor para que procese el rango del PDF y genere una "Píldora".
3.  **Consultar**: El desarrollo de código se basa únicamente en las Píldoras de Conocimiento, evitando leer el PDF completo en cada interacción.

---
