# MacroPad Spec

## Purpose
A custom STM32F411 USB macropad with register-level firmware, hand-written USB HID descriptors, and a custom PCB. I'm building it to speed up my daily workflow across work, school, and projects, and to learn embedded systems and hardware/software integration end to end.

## v1 — Must have
- 12 keys, 3×4 diode matrix, hot-swap on the final PCB
- 1 EC11 rotary encoder with push switch (default: volume)
- USB-C, full-speed USB HID keyboard (6KRO boot protocol, then NKRO)
- Composite device: keyboard + consumer control (media, volume)
- Keymap with layers and macros
- Custom PCB, STM32F411

## v2 — Stretch
- SSD1306 OLED showing layer/volume
- Host configurator + flash-stored keymap (over a raw HID interface)
- DFU firmware updates from the configurator
- Own USB device stack replacing TinyUSB

## Non-goals
- No wireless / battery
- No QMK/ZMK or other keyboard firmware frameworks; TinyUSB is the only third-party firmware library
- No vendor HAL or CubeMX-generated code

## Measurable targets
- Matrix scan rate: 1 kHz
- Key-to-USB-report latency: ≤ 5 ms worst case (eager debounce), ≤ 10 ms (integrator)
  - Measured from switch contact to HID report queued, plus 1 ms for the USB polling interval
- Zero chatter across 1,000 presses per key