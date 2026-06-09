# Ender 3 Neo - Firmware Flashing Instructions

## Hardware
- **Motherboard:** Creality E3 Free-runs Silent (CR4NTXXC10) with STM32F401RET6
- **Stepper Drivers:** TMC2209 (X, Y, Z, E0)
- **Probe:** BLTouch
- **Display:** 12864 Monochrome LCD (RET6)

## Firmware Location on SD Card
⚠️ **IMPORTANT:** The compiled firmware `.bin` file MUST be placed in a specific folder structure:

```
SD_CARD_ROOT/
├── STM32F4_UPDATE/
│   └── firmware-XXXXXX.bin  ← Put the .bin file HERE
```

**NOT** in the root of the SD card.

## Flashing Steps
1. Copy the compiled `.bin` file from `.pio/build/STM32F401RE_freeruns/` 
2. Create folder `STM32F4_UPDATE` on SD card root (if doesn't exist)
3. Place `.bin` inside `STM32F4_UPDATE/` folder
4. Safely eject SD card from computer
5. Insert SD into **powered-off** Ender 3 Neo
6. Power on the printer
7. Firmware will flash automatically
8. After flashing completes, the `.bin` file will be automatically deleted from the SD card
9. Remove SD card and restart printer

## Configuration Notes
- **PAUSE behavior:** Retracts 2mm at 25 mm/s, parks nozzle at X=-30 (outside bed), maintains XYZ motors powered
- **Change Filament:** Same sequence as PAUSE + unload 100mm + load new filament + purge 50mm
- **Bed Leveling:** Bilinear auto-leveling with 5×5 mesh
- **Nozzle Park Point:** {-30, 225, 20} - completely outside print bed area

## Build Environment
- **PlatformIO Environment:** `STM32F401RE_freeruns`
- **Framework:** Arduino STM32
- **Compiler:** ARM GCC

---
*Last updated: 2026-06-09*
