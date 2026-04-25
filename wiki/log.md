# Wiki Log

## 🗓 2026-04-22
### Refined Ingestion & Synthesis
- **Detailed Component Research:**
    - Verified **CAN** transceiver requirements: VCC=5V, VIO=3.3V (e.g., TJA1051).
    - Identified standard **Analog Scaling** for 0-10V: 22k/10k divider for a 3.125V peak.
    - Confirmed **Ethernet RMII** pinout compatibility with Nucleo-144 layout.
    - Specified **SPI Flash** as standard 4-wire SPI (SST25VF040B) on SPI2.
- **Wiki Updates:**
    - Updated `Microcontroller.md` with refined pin assignments for CAN, SPI, and Analog.
    - Updated `Hardware_Architecture.md` with specific resistor values for analog scaling and transceiver power requirements.
    - Verified 4-layer stack-up and copper clearance rules in `Reference_Designs.md`.
- **Key Decisions:**
    - Selected **PD0/PD1** for CAN1 and **PB5/PB13** for CAN2 to avoid RMII/USB conflicts.
    - Recommended **Op-Amp buffers** for analog inputs to ensure signal integrity as per best practices.
- **Component Documentation:**
    - Documented **SST25VF040B** SPI Flash characteristics and integration rules.
    - Created `Components.md` to track specific IC selections.
    - Investigated **PB2/BOOT1** role; confirmed it is **not used** for boot in STM32F746 (replaced by Option Bytes).
- **Boot Strategy:**
    - Documented **Manual Boot Selection** circuit using a 10k pull-down and push-button to 3.3V for Bootloader access.
- **Interface Guidelines:**
    - Compiled **SPI Protocol** recommendations, focusing on series termination, pull-ups for CS, and high-speed layout (50 MHz).
    - Added **Advanced SPI** tuning: GPIO drive strength, software NSS management, and industrial robustness (filtering/isolation).

### 🗓 2026-04-22 (Continued)
- **USB Power Compatibility:**
    - Investigated **CN13 (User USB)** power capabilities. 
    - **Outcome:** Confirmed it **cannot** power the Nucleo board; doing so risks current injection and MCU damage.
    - **Documentation:** Created detailed analysis in `outputs/CN13_Power_Compatibility.md` and updated `wiki/Reference_Designs.md` with custom design fix (Schottky diode/Power switch).

### 🗓 2026-04-23
- **SPI Protocol Consolidation:**
    - Synthesized disparate SPI recommendations into `outputs/SPI_Protocol_Synthesis.md`.
    - Created dedicated `wiki/SPI_Protocol.md` for fast access to high-signal implementation data.
    - Integrated frequency limits (54MHz/27MHz), SI termination (22-33 Ohm), and pin conflict awareness (Ethernet/LEDs).
    - Verified **SST25VF040B** SPI Mode 0/3 compatibility and pull-up requirements.
