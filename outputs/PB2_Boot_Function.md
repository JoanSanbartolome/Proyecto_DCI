# PB2 Pin and Boot Configuration: STM32F746ZG

This document clarifies the role of the PB2 pin during the startup and boot sequence of the STM32F746ZG microcontroller.

## 📝 Findings
Unlike previous generations (such as the STM32F2 or STM32F4 series), the **STM32F746ZG does not use PB2 as a physical BOOT1 pin.**

### 1. Boot Selection Mechanism
- **Hardware Pin:** Only the **BOOT0** pin is used as a physical hardware signal to influence the boot process.
- **Software Configuration:** The functionality previously handled by the `BOOT1` pin is replaced by **BOOT_ADD0** and **BOOT_ADD1 option bytes**. These are user-programmable bits in the flash memory that define the boot address when BOOT0 is 0 or 1.

### 2. PB2 Specific Function
- For the STM32F746ZG, **PB2 is a standard General Purpose I/O (GPIO).**
- It can be used for any supported alternate function (such as SPI1_MOSI, I2S1_SD, TIM2_CH4, etc.) without impacting the boot mode.
- In the Nucleo-F746ZG reference design, solder bridges for BOOT1 (SB142/SB152) are labeled as "Only for F2 and F4 Series," confirming they are not applicable to the F7 variant.

## ⚠️ Hardware Design Implications
- **BOOT0 Pin:** Must still be pulled **LOW** for normal boot from Flash or **HIGH** to enter the System Bootloader (for UART/USB programming).
- **PB2 Pin:** Can be used freely in your design (e.g., as part of the expansion header or for other peripherals) without worrying about "BOOT1" logic levels during reset.

[Source: stm32f746zg.pdf Section 3.15, dm00244518.pdf Section 7.12]
