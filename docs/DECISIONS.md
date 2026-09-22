# Design Decisions

One entry per non-obvious choice. Newest at the bottom.

---

## 001 — MCU: STM32F411 over RP2040
**Date:** 2026-09-22  
**Decision:** Use the STM32F411CEU6 (Cortex-M4F, 100 MHz, 512 KB flash, 128 KB SRAM) on a WeAct Black Pill for development and on the custom PCB.  
**Alternatives considered:** RP2040 (Raspberry Pi Pico), ESP32-S3, ATmega32U4.  
**Why:**
- STM32 is common in industry, including at companies I'm targeting, so the skills carry over directly.
- Bring-up is harder than on the RP2040: an external crystal, a PLL clock tree, flash wait states, and a dense reference manual. Doing that by hand is the point of the project.
- Its peripherals fit the design: USB OTG_FS for HID, timers with a hardware encoder mode for the EC11, DMA for the OLED, and a 128 KB flash sector for storing config.
- The RP2040 would be easier (drag-and-drop UF2 flashing, a friendlier SDK). Its standout feature, PIO, is unique to that chip and transfers less to other work.
- The ESP32 brings Wi-Fi and Bluetooth, which the spec lists as non-goals. The ATmega32U4 is well understood but dated, with only 2.5 KB of RAM.
**Revisit if:** the F411 is out of stock at JLCPCB when I order the PCB in Phase 4.  

---

## 002 — Register-level firmware, no HAL or CubeMX code
**Date:** 2026-09-22  
**Decision:** Write drivers (GPIO, RCC, timers, I2C, flash) by accessing registers directly. Use only the CMSIS core and ST device headers for register definitions. No STM32 HAL or LL libraries, and no CubeMX-generated code.  
**Alternatives considered:** STM32 HAL + CubeMX, STM32 LL drivers, libopencm3.   
**Why:**
- The HAL hides the details interviewers ask about: clock enables, alternate functions, interrupt flags, the startup sequence. Writing them myself means I can explain every line.
- Every driver is traceable to a section of RM0383, which forces me to learn to read a reference manual. That's a core embedded skill.
- The code stays small and readable, with no generated boilerplate.
- CMSIS headers are allowed because they only name registers; they don't hide behavior. The first blinky uses hand-defined addresses to prove I understand the memory map, then switches to the CMSIS headers.
- CubeMX's pin planner may still be used in Phase 4 to check for alternate-function conflicts, but none of its generated code.
**Revisit if:** a peripheral's register-level driver would cost weeks that are better spent elsewhere (for example, the USB OTG core, which is handled by decision 003).  

---

## 003 — TinyUSB for the USB stack (own stack as a stretch goal)
**Date:** 2026-09-22  
**Decision:** Use TinyUSB as the USB device stack on the OTG_FS peripheral. Write every descriptor by hand (device, configuration, interface, HID report).  
**Alternatives considered:** ST USB Device library (part of the HAL ecosystem), writing my own USB device stack from scratch.  
**Why:**
- The STM32 OTG core is complex: FIFO allocation, endpoint state machines, many interrupt sources. Writing a stack from scratch could take more than all of Phase 3's four weeks, which would put the November milestone at risk.
- TinyUSB is widely used, MIT-licensed, and supports the F411's OTG_FS directly. Its code is readable when I need to debug enumeration.
- The ST USB library would pull in the HAL, which contradicts decision 002.
- The interesting parts stay mine: descriptors, report formats, NKRO, the composite device layout, and latency measurement.
- The spec names TinyUSB as the only third-party firmware library.
**Revisit if:** v1 is complete with time left. Replacing TinyUSB with my own stack is listed as a v2 stretch goal.  

---

## 004 — Dev board source: WeAct Black Pill from DFRobot
**Date:** 2026-09-22   
**Decision:** Buy two WeAct Black Pill V3.0 boards (STM32F411CEU6, 25 MHz HSE) from DFRobot.  
**Alternatives considered:** Unbranded Black Pill listings on Amazon, WeAct's official AliExpress store, ST NUCLEO-F411RE from DigiKey or Mouser.  
**Why:**
- Counterfeit and relabeled STM32s are common on marketplaces. A fake chip can cause bugs that look like my own firmware errors, which would waste hours of debugging.
- The Amazon listing I considered listed 256 KB of flash. A real F411CEU6 has 512 KB, and decision 001's config storage needs the last 128 KB sector, which only exists on a 512 KB part.
- DFRobot is an established distributor. Its listing has the correct specs, links WeAct's GitHub, and states in writing that it uses original ST chips.
- The Nucleo-F411RE carries the least risk, but it's a large board that doesn't match the Black Pill layout I'll mirror on my PCB.
- I bought two so a damaged board doesn't stall the project while I wait for international shipping. (See decision 005 for how the second board is used.)  

**Verification on arrival:** read `DBGMCU_IDCODE` (device ID 0x431) and the flash-size register (512 KB) over SWD, and check the chip marking. Results are logged here.  
**Revisit if:** either board fails silicon verification.  