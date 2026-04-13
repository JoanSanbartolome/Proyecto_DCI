# Checklist Profesional Altium: Proyecto DCI 2025/2026

## 1. ESQUEMÁTICO (Fase Inicial Paralela)
### Miembro A: Sistema Base y Digital
- [ ] **(!) Definir Hoja Top-Level y estructura jerárquica del proyecto**
- [ ] Importar STM32F746ZG y eliminar zócalo (conexión directa)
- [ ] Eliminar bloque ST-LINK_V2 y componentes redundantes de la NUCLEO
- [ ] **(!) Diseñar bloque SWD** (Tira de pines 100mils para depuración externa)
- [ ] Diseñar bloque Ethernet (RJ45 con LEDs + Magnético externo)
- [ ] Diseñar bloque Micro-USB
- [ ] Diseñar bloque Memoria SPI Flash (>2Mb)

### Miembro B: Señal Mixta y Expansión
- [ ] **(!) Crear Hoja Jerárquica Compleja para Transceptores CAN**
- [ ] Diseñar bloque CAN (2 transceptores con jumper/switch de terminación)
- [ ] **(!) Crear Hoja Jerárquica Compleja para Acondicionadores 0-10V**
- [ ] Diseñar bloque Acondicionador Analógico (Protección + Divisor/OpAmp)
- [ ] Diseñar bloque Conector Expansión (16 señales GPIO)

### Tareas Intermedias (Sincronización)
- [ ] Anotación de componentes (Tools -> Annotation)
- [ ] Verificación de Footprints (Footprint Manager)
- [ ] Ejecutar ERC (Electrical Rule Check) y corregir warnings de nets flotantes

## 2. REGLAS DE DISEÑO (Directivas)
- [ ] **(!) Miembro A:** Definir clases de red para señales diferenciales (USB_D, ETH_TX/RX)
- [ ] **Miembro B:** Definir clases de red para potencia (VCC, GND) y señales analógicas
- [ ] Insertar Directivas de Par Diferencial en el esquemático
- [ ] Añadir Parameter Set Directives para anchos de pista específicos (Power vs Signal) en el esquemático
- [ ] Configurar reglas de Clearance generales y específicas para el conector RJ45

## 3. LAYOUT (Emplazamiento)
- [ ] **(!) Definir Board Shape y Stack-up** (4 capas, máx 1.6mm, perfil Lab Circuits)
- [ ] Configurar capas de plano internas (GND y PWR) - No usar capas de señal
- [ ] **(!) Colocar 4 taladros de sujeción** (3.5mm/6.5mm corona) vinculados a net EARTH
- [ ] Emplazamiento de componentes críticos (MCU, Cristales, Ethernet, USB)
- [ ] **(!) Validar cambio sustancial de formato** respecto a la NUCLEO original
- [ ] Definir polígono de recorte (Keepout) bajo el magnético Ethernet (todas las capas)

## 4. ENRUTADO (Routing)
- [ ] Enrutar pares diferenciales (Ethernet y USB) con control de impedancia
- [ ] Enrutar buses de memoria SPI Flash (Longitudes emparejadas si es necesario)
- [ ] Enrutar señales analógicas (Miembro B) aisladas de ruido digital
- [ ] Configurar y verter planos de alimentación (PWR partido si hay múltiples tensiones)
- [ ] Cosido de vías de masa (Via Stitching) para integridad de señal

## 5. VALIDACIÓN Y SALIDA
- [ ] Ejecutar DRC (Design Rule Check) final - Cero errores
- [ ] Verificar conexión de EARTH a chasis/taladros
- [ ] Generar Output Job File (.OutJob)
- [ ] **Entregable:** Generar Gerbers y Ficheros de Taladros (NC Drill)
- [ ] **Entregable:** Generar Bill of Materials (BOM) con proveedores
- [ ] **Entregable:** Generar Fichero Pick&Place
- [ ] **Entregable:** Exportar PDF del Stack-up detallado
- [ ] Generar Informe final (Justificación de decisiones y capturas de simulación)
