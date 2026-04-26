# Voron 2.4 350mm — Klipper Config

## Hardware

| Component | Detail |
|---|---|
| Frame | Voron 2.4 350mm |
| Main MCU | BigTreeTech SKR 1.4 (LPC1768) |
| Z MCU | BigTreeTech SKR 1.4 (LPC1768) — second board |
| Toolhead MCU | BTT EBBCan 36 (STM32G0B1) via CAN bus |
| Extruder | Clockwork 2 (CW2) |
| Hotend | E3D Revo High Flow 60W, 0.4mm nozzle |
| Probe | Voron TAP |
| XY Motors | OMC 17HS19-2004S1 (24V via TMC2209) |
| Z Motors | OMC 17HS19-2004S1 (24V via TMC2209) |
| All Drivers | TMC2209 UART |
| Bed | 350mm PEI flex plate |
| Display | KlipperScreen touchscreen |
| LEDs | StealthBurner RGBW, klipper-led_effect |
| Filters | Single Nevermore fan |

## Key IDs

| Item | Value |
|---|---|
| Main MCU serial | `/dev/serial/by-id/usb-Klipper_lpc1768_1A90000CE3A448AF620C3E5DC32000F5-if00` |
| Z MCU serial | `/dev/serial/by-id/usb-Klipper_lpc1768_1290FF01809C48AFCD1F3A5DC12000F5-if00` |
| EBBCan CAN UUID | `9f4710a71f4e` |
| IP address | 192.168.1.38 |
| Hostname | voron24.local |

## Software

- Klipper + Moonraker + Mainsail
- KlipperScreen
- Klippain ShakeTune
- TMC Autotune (klipper_tmc_autotune)
- klipper-led_effect
- Spoolman (filament tracking — server at 192.168.1.175:7912)

## Key Tuning Values — Siraya Tech HT-HF ABS

| Setting | Value |
|---|---|
| Nozzle temp | 246°C |
| Bed temp | 110°C (first layer) / 105°C (subsequent) |
| Pressure advance | 0.042 |
| Flow ratio | 0.93 |
| Max volumetric speed | 14 mm³/s |
| Input shaper X | EI @ 45.6Hz, damping 0.045 |
| Input shaper Y | MZV @ 36.4Hz, damping 0.053 |
| Max accel | 3500 mm/s² |
| XY shrinkage | 99.4% |
| Z shrinkage | 99.2% |
| Z offset | -1.155 |

## Config File Map

| File | Purpose |
|---|---|
| `printer.cfg` | Main config — kinematics, steppers, input shaper, autotune, QGL |
| `common_macros.cfg` | Shared macros — PRINT_START, PRINT_END, parking, filament |
| `macros.cfg` | V2.4-specific — SMART_QGL, NEVERMORE, AUTO_MODE, led_effect hooks |
| `EBBCan.cfg` | Toolhead MCU — extruder, hotend, ADXL |
| `probe.cfg` | TAP probe config |
| `fans.cfg` | Part fan, hotend fan, Nevermore, electronics fans |
| `bed_heater.cfg` | Heated bed config |
| `thermistor.cfg` | Chamber/EBBCan/RPi temperature sensors |
| `stealthburner_leds.cfg` | StealthBurner RGBW LED states |
| `led.cfg` | klipper-led_effect animations |
| `filament_runout.cfg` | BTT filament motion sensor |
| `config_backup.cfg` | GitHub autocommit shell command + macro |
| `moonraker.conf` | Moonraker server, update manager, Spoolman, led_effect |
| `KlipperScreen.conf` | KlipperScreen display settings |
| `autocommit.sh` | Git push script — runs hourly via cron + after every print |

## Recovery

If rebuilding from scratch:
1. Flash Klipper to both SKR 1.4 boards (LPC1768, 16KiB bootloader, USB)
2. Flash Katapult + Klipper to EBBCan via USB DFU mode, then switch to CAN
3. Install KIAUH → Klipper + Moonraker + Mainsail + KlipperScreen
4. Install ShakeTune, TMC autotune, led_effect via their install scripts
5. Clone this repo to `~/printer_data/config/`
6. Verify CAN UUID in EBBCan.cfg (run `~/klippy-env/bin/python ~/klipper/scripts/canbus_query.py can0`)
7. Verify serial IDs in printer.cfg (check `/dev/serial/by-id/`)
8. Run PID calibration, probe calibration, QGL, ShakeTune, PA calibration

## Notes

- Two SKR 1.4 boards: `mcu` for XY/extruder, `z` for Z steppers
- AUTO_MODE macro adjusts acceleration based on actual print footprint size
- SMART_QGL skips QUAD_GANTRY_LEVEL if already done this session (saves ~2min per print)
- SMART_MESH skips bed mesh if bed temp is within 5°C of last mesh (saves ~3min per print)

## Related Repos

- OrcaSlicer profiles: https://github.com/ken-alt/orcaslicer_profiles
- Voron Trident config: https://github.com/ken-alt/trident_klipper_config
