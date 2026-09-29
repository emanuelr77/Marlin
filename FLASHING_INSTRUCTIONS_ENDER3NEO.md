# Ender 3 Neo - Firmware Flashing Instructions

## Hardware
- **Motherboard:** Creality E3 Free-runs Silent (CR4NTXXC10) with STM32F401RET6
- **Stepper Drivers:** TMC2209 (X, Y, Z, E0)
- **Probe:** BLTouch (also used for Z homing)
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

## After Flashing

`EEPROM_INIT_NOW` is enabled, so **every new build resets the EEPROM to the values in `Configuration.h`**.
Anything saved only with `M500` (mesh, tweaked Z offset, bed PID) is lost on reflash.

**Already in the firmware (no need to reconfigure):**
- E steps: 415 steps/mm
- Probe offset: X -39, Y -10, **Z -1.53**
- Hotend PID (from autotune): Kp 20.4, Ki 1.57, Kd 66.1
- Feedrates, accelerations, retraction, park position, pause timeout
- PLA preheat: 185 °C hotend / 60 °C bed (bed also preheats to 60 °C before leveling)

**Checklist:**
1. **Verify the new firmware:** check the build date in the Info menu, or send `M503` and confirm `M851 ... Z-1.53` and `M92 ... E415`.
2. **Tram the bed** with the Assisted Tramming (G35) wizard — only if the bed was adjusted.
3. **Create a new mesh:**
   ```
   G28
   G29      ; bed preheats to 60 °C, probes a 4x4 grid
   M500     ; save mesh to EEPROM
   ```
4. **Check the first layer.** If the Z offset needs correcting (babystepping or Probe Offset Wizard), save with `M500`
   **and update `NOZZLE_TO_PROBE_OFFSET` Z in `Configuration.h`**, or the next reflash reverts it to -1.53.
5. **Slicer start G-code:** `G28` disables bed leveling (`RESTORE_LEVELING_AFTER_G28` / `ENABLE_LEVELING_AFTER_G28` are off).
   Add after `G28`:
   - `M420 S1` to use the saved mesh (recommended), or
   - `G29` to probe a fresh mesh every print.

**Optional:** bed PID values are generic defaults (462.10 / 85.47 / 624.59). If the bed temperature oscillates,
run `M303 E-1 S60 C8` and copy the result into `DEFAULT_BED_KP/KI/KD` in `Configuration.h`.

## Configuration Notes
- **Pause (M125 / LCD):** Retracts 2 mm at 25 mm/s, parks the nozzle outside the bed, keeps XYZ motors powered.
- **Change Filament (M600):**
  1. Retract 2 mm, raise Z (at least 20 mm, or +2 mm), park at X -20 / Y 225
  2. Unload (same as the Orca end G-code): wait 15 s, then pull 100 mm in one fast move (25 mm/s, 500 mm/s²) to form a clean tip
  3. Wait for the user; hotend stays hot for **300 s** (`PAUSE_PARK_NOZZLE_TIMEOUT`), then turns off and reheats on button press
  4. Load is manual (fast load length = 0): push the filament to the nozzle, then confirm
  5. Purge 50 mm at 3 mm/s, with "Purge more / Continue" menu
  6. Return to the print position and resume
- **Hotter purge for filament change** (optional, from the slicer at the color-change layer):
  ```
  M104 S230   ; unload/load/purge at 230 °C
  M600
  M104 S185   ; back to print temp (first lines after resume print slightly hotter)
  ```
- **Bed Leveling:** Bilinear auto-leveling with 4×4 mesh
- **Nozzle Park Point:** `{ -20, Y_MAX_POS - 10, 20 }` = X -20, Y 225, Z min 20 mm — outside the print area

## Build Environment
- **PlatformIO Environment:** `STM32F401RE_freeruns`
- **Framework:** Arduino STM32
- **Compiler:** ARM GCC

---
*Last updated: 2026-09-29*
