# Phase 0 reading notes

## 1. Memory map
Flash start: Beginning of Sector 0; 0x0800 0000
SRAM start: 0x2000 0000
Peripheral region: GPIOA is at 0x4002 0000 and TIM2 at 0x4000 0000
Source: Figure 3.3 Reference Manual, Figure 14 Datasheet

## 2. Base addresses
RCC: 0x4002 3800
GPIOC: 0x4002 0800
Source: 2.3 Table 1 Reference Manual

## 3. GPIOC clock enable
Register and bit: Bit 2; Address offset 0x30
What happens if GPIOC is written before its clock is on: The write is silently ignored. GPIOC's registers are flip-flops that only capture
data on a clock edge; with GPIOCEN = 0 they get no clock, so MODER/ODR stay at
reset values. No fault is raised — the LED just doesn't work.
Source: RM0383 Rev 4 §6.3.9 p.118; reasoning from how clocked registers work

## 4. MODER, ODR, BSRR
#### MODER: 
- __Address Offset__: 0x00
- __Bits Per Pin__: 2
- __What the Values Mean__: 
    - These bits are written by software to configure the I/O direction mode.
    - 00: Input (reset state)
    - 01: General purpose output mode
    - 10: Alternate function mode
    - 11: Analog mode
- __Which Bits Control Pin 13__: \[26:27\]
#### ODR: 
- __Address Offset__: 0x14
- __Bits Per Pin__: 1
- __What the Values Mean__: 
    - Bits \[31:16\] are reserved; Bits \[15:0\] ODRy: Port Output Data \(y = 0... 15\)
    - Can be written by software.
#### BSRR: 
- __Address Offset__: 0x18
- __Bits Per Pin__: 1
- __What the Values Mean__: 
    - Note: These bits are write-only and can be accessed in word, half-word, or byte mode. A read to these bits returns 0x0000
    - Bits \[31:16\] Port x reset bit y \(y = 0... 15\)
        - 0: No action on the corresponding ODRx bit
        - 1: Resets the corresponding ODRx bit
    - Bits \[15:0\] Port x set bit y \(y = 0... 15\)
        - 0: No action on the corresponding ODRx bit
        - 0: Sets the corresponding ODRx bit
    - Note: If both BSx and BRx are set: BSx has priority.
__Why BSRR beats read-modify-write on ODR__: Read-modify-write on ODR is three steps (read, modify, write). If an interrupt changes ODR between the read and the write, the stale copy overwrites it: a
lost update. BSRR needs no read: writing 1 sets/resets only the named pin,
writing 0 does nothing, so other pins are untouched. It's a single store,
which is atomic, so there's no window for an interrupt. (BSRR reads as 0x0000.)
Source: RM0383 Rev 4 §8.4.6 p.160, §8.4.7 p.161

## 5. PC13 LED (schematic)
How the LED is wired: 3.3V -> R5 1K Resistor -> LED -> PC13
Why writing 0 turns it on: It is wired from VDD to pin so writing zero acts as GND, pulling the current from high potential to low potential which turns the LED on due to the voltage difference; writing one is like having 3.3V on each side of the LED, there is no current in that case.
Source: Indicator light section in schematic.

## 6. Clock path HSE → PLL → SYSCLK

HSE = 25 MHz (WeAct V3.0 schematic, Y2 "25MHZ 9PF"; HSE OSC supports 4–26 MHz)

__Path (system clock)__:
25 MHz crystal → HSE OSC → PLLSRC mux (selects HSE) → /M → VCO (×N) → /P → PLLCLK → SW mux → SYSCLK

__Path (USB clock)__:
VCO → /Q → PLL48CK → USB OTG FS (must be exactly 48 MHz)

Also: HSE can go straight to the SW mux (SW = 01) → SYSCLK = 25 MHz, no PLL.
Useful bring-up step to prove the crystal works before adding the PLL.

__Frequencies with M = 25, N = 192, P = 2, Q = 4__:
- After /M:   25 MHz / 25   = 1 MHz     (must be 1–2 MHz; ST recommends 2 MHz for
                                          lower jitter, but 25/2 isn't an integer M)
- After ×N:   1 MHz × 192   = 192 MHz   (VCO, must be 100–432 MHz)
- After /P:   192 MHz / 2   = 96 MHz    → SYSCLK (max 100 MHz)
- After /Q:   192 MHz / 4   = 48 MHz    → USB

__Which register holds PLLM, PLLN, PLLP, PLLQ__:
RCC_PLLCFGR (offset 0x04)
- PLLM:   bits 5:0
- PLLN:   bits 14:6
- PLLP:   bits 17:16  (encoded: P=2 is written as 00; P=4 → 01, P=6 → 10, P=8 → 11)
- PLLSRC: bit  22     (1 = HSE, 0 = HSI)
- PLLQ:   bits 27:24
Only writable while the PLL is off.

__The SW mux is RCC_CFGR (offset 0x08)__:
- SW  (bits 1:0) = 10 selects PLL  (00 = HSI, 01 = HSE)
- SWS (bits 3:2) = hardware confirms the switch; wait until it reads 10
Reset default is HSI (16 MHz), so code runs at 16 MHz until the clock setup runs.

__Why 96 MHz instead of 100__:
USB needs exactly 48 MHz, and /P and /Q divide the same VCO. 192 MHz divides
evenly to 96 (÷2) and 48 (÷4); no VCO gives both 100 MHz and exactly 48 MHz.

Source: RM0383 Rev 4, Figure 12 p. 94; §6.3.2 p. 105; §6.3.3 p. 107

## 7. Flash wait states
Wait states needed at 96 MHz, 3.3 V: 3 WS \(4 CPU cycles\)
Source: RM0383 Rev 4, §3.4.1, Table 5 "Number of wait states according to CPU clock (HCLK) frequency", p. 45
