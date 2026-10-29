# Gnimu KiCad library

The symbols and footprints shared by every board in this repository. All of them are through-hole: modules that solder straight onto a carrier, a connector, or wire pads.

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
| `Pololu_D24V7Fx` | Pololu D24V7Fx step-down regulator: VIN, GND, VOUT. The D24V7F5 is the 5V version. |
| `XL63070_Module` | XL63070 buck-boost module (TPS63070). EN high is on, held by the module's own pull-up. |
| `Adafruit_CAN_Pal` | Adafruit CAN Pal CAN transceiver breakout. TX and RX go to the MCU's CAN controller; S is the silent/standby control. |
| `OBD2_J1962_Plug_16P` | J1962 OBD-II plug, 16 pins. Only CAN_H (6), CAN_L (14), GND (4, 5) and +12V (16) are used; the rest are no-connects. |

## Footprints: `Gnimu.pretty`

| Footprint | Part |
|---|---|
| `XIAO_nRF52840_THT` | Seeed XIAO nRF52840 Sense, soldered by its edge pins. |
| `XIAO_ESP32C6_THT` | Seeed XIAO ESP32C6, soldered by its edge pins. The antenna is at the end opposite the USB port; keep copper away from it. |
| `Pololu_D24V7Fx_THT` | Pololu D24V7Fx step-down regulator, on a 1×3 header. Pads 1 VIN, 2 GND (square), 3 VOUT, left to right with the pins nearest you and the components facing up. The outline shows how far the regulator board reaches past its pins. |
| `XL63070_THT` | XL63070 buck-boost module. |
| `Adafruit_CAN_Pal_THT` | Adafruit CAN Pal breakout. |
| `OBD2_J1962_Plug_Straight_16P` | The back of a straight-pin J1962 OBD-II plug: 16 plated oval slots on a 4.0 mm × 8.6 mm grid, with the plug's flange and rear trench drawn on `Dwgs.User`. Slot sizes follow the RejsaCAN v3.x plug gerbers. |
| `Holes_1x01_P2.54mm` to `Holes_1x06_P2.54mm` | Rows of one to six plated holes on a 2.54mm pitch, for soldering wire leads directly to the board. |

## 3D models: `Gnimu.3dshapes`

| Model | Used by |
|---|---|
| `Pololu_D24V7Fx.step` | `Pololu_D24V7Fx_THT`, raised 2.5mm for the header-pin spacer. Pololu's own model, from the [product page](https://www.pololu.com/product/5593/resources). |

The footprints find their models through `${KIPRJMOD}/../../../libraries/Gnimu.3dshapes/`, so the 3D view works for projects at the usual depth under `boards/`.

The XIAO footprints cover the edge pins only. The battery pads on the underside of a XIAO are not included; carriers that need them, such as the Gnimu-CAN LiPo carrier, bring them out to a separate pair of wire pads.

"Adafruit" and "CAN Pal" belong to Adafruit Industries, "Seeed" and "XIAO" to Seeed Studio, and "Pololu" to Pololu Corporation. The parts here are independent drawings for mounting those modules, and are not affiliated with or endorsed by any of them. The Pololu 3D model is Pololu's work, included as published.
