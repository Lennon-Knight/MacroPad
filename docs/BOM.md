# Bill of Materials

Ordered 2026-09-22. Prices before tax - Rounded to the nearest dollar - Shipping included.
Status: Ordered → Arrived → Verified (tested/inspected).

## Project parts

| Item | Qty | Source | Price | Phase | Status |
|---|---|---|---|---|---|
| WeAct Black Pill V3.0, STM32F411CEU6 (25 MHz HSE, 512 KB flash) | 2 | DFRobot | $48.00 ($16.50 ea + $15 Shipping) | 1 | Ordered |
| 1N4148 diodes, through-hole | 125 | Amazon | $6 | 2 | Ordered |
| EC11 rotary encoder w/ push switch + knobs (WWZMDiB) | 6 | Amazon | $9 | 5 | Ordered |
| SSD1306 0.96" 128×64 OLED, 4-pin I2C (Hosyond) | 5 | Amazon | $15 | 8 | Ordered |
| MX keycaps, R4 uniform | 20 | Amazon | $12 | 2 | Ordered |
| Akko V3 Cream Yellow Pro switches (5-pin, linear, 50 gf) | 45 | Already owned | — | 2 | Owned |

## Prototyping

| Item | Qty | Source | Price | Phase | Status |
|---|---|---|---|---|---|
| Perfboard kit, 174-pc (FR-4, incl. female headers) | 1 | Amazon | $19 | 2 | Ordered |
| Half-size breadboards, 400-pin | 2 | Amazon | $7 | 1 | Ordered |
| Dupont jumpers (M-M, M-F) | 1 set | Amazon | $7 | 1 | Ordered |
| 22 AWG solid-core wire (red, black) | 1 | Amazon | $8 | 2 | Ordered |
| Anker USB-C to USB-C cable, 60 W / 3 A (2-pack) | 1 | Amazon | $10 | 0 | Ordered |

## Tools (reusable beyond this project)

| Item | Qty | Source | Price | Phase | Status |
|---|---|---|---|---|---|
| Pinecil V2 soldering iron | 1 | Amazon | $40 | 1 | Ordered |
| Amazon Basics 65W GaN USB-C PD charger (Pinecil PSU) | 1 | Amazon | $17 | 1 | Ordered |
| Iron holder w/ brass tip cleaner | 1 | Amazon | $12 | 1 | Ordered |
| 8-ch 24 MHz logic analyzer, FX2 (HiLetgo) | 1 | Amazon | $13 | 1 | Ordered |
| Test hook clips, 12-pc (RuiLing) | 1 | Amazon | $8 | 2 | Ordered |
| USB-UART adapter (1.8/2.5/3.3/5 V jumper) | 1 | Amazon | $15 | 1 | Ordered |
| Multimeter | 1 | Amazon | $15 | 1 | Ordered |

## Consumables

| Item | Qty | Source | Price | Phase | Status |
|---|---|---|---|---|---|
| AIM 63/37 rosin-core solder, 0.8 mm, 0.25 lb | 1 | Amazon | $17 | 1 | Ordered |
| Flux paste + solder wick (Lesnow) | 1 | Amazon | $9 | 1 | Ordered |
| 99% isopropyl alcohol | 1 | Amazon | $5 | 1 | Ordered |
| Cleaning brush | 1 | Amazon | $7 | 1 | Ordered |

## Totals
- Project parts: ~$90
- Prototyping: ~$51
- Tools + consumables: ~$158
- **Total: ~$299** 

## Notes
- **No ST-LINK:** the second Black Pill runs CMSIS-DAP firmware as the SWD debugger (see DECISIONS.md).
- **On arrival:** verify each Black Pill's silicon over SWD (`DBGMCU_IDCODE` device ID 0x431, flash size register = 512 KB).

## Planned, not yet ordered
| Item | Source | Phase |
|---|---|---|
| Name-brand no-clean flux (MG Chemicals / Chip Quik) | DigiKey | before 6 |
| 3×4 switch plate, 19.05 mm pitch | 3D print | 2 |
| Custom PCB + assembly | JLCPCB | 4 (early Dec) |