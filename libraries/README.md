# Gnimu KiCad library

The symbols and footprints shared by every board in this repository. All of them are through-hole parts for modules that solder straight onto a carrier.

Each project under [`boards/`](../boards/) loads this library under the nickname `Gnimu`, through its own `sym-lib-table` and `fp-lib-table`:

```
${KIPRJMOD}/../../../libraries/Gnimu.kicad_sym
${KIPRJMOD}/../../../libraries/Gnimu.pretty
```

The paths are relative to the project, so the library works from a fresh clone with nothing to install. A new project needs the same two entries, adjusted if it sits at a different depth.

## Symbols: `Gnimu.kicad_sym`

| Symbol | Part |
|---|---|
| `XIAO_nRF52840_Sense` | Seeed XIAO nRF52840 Sense, 14 edge pins only. |
| `XIAO_ESP32C6` | Seeed XIAO ESP32C6, 14 edge pins only. Pin names include the ESP32-C6 GPIO numbers. |
| `XL63070_Module` | XL63070 buck-boost module (TPS63070). EN high is on, held by the module's own pull-up. |
| `Adafruit_CAN_Pal` | Adafruit CAN Pal CAN transceiver breakout. TX and RX go to the MCU's CAN controller; S is the silent/standby control. |

## Footprints: `Gnimu.pretty`

| Footprint | Part |
|---|---|
| `XIAO_nRF52840_THT` | Seeed XIAO nRF52840 Sense, soldered by its edge pins. |
| `XIAO_ESP32C6_THT` | Seeed XIAO ESP32C6, soldered by its edge pins. |
| `XL63070_THT` | XL63070 buck-boost module. |
| `Adafruit_CAN_Pal_THT` | Adafruit CAN Pal breakout. |
| `Holes_1x01_P2.54mm` to `Holes_1x06_P2.54mm` | Rows of one to six plated holes on a 2.54mm pitch, for soldering wire leads directly to the board. |

The XIAO footprints cover the edge pins only. The battery pads on the underside of a XIAO are not included; the carrier brings them out to a separate pair of wire pads.

"Adafruit" and "CAN Pal" belong to Adafruit Industries, and "Seeed" and "XIAO" to Seeed Studio. The parts here are independent drawings for mounting those modules, and are not affiliated with or endorsed by either company.
