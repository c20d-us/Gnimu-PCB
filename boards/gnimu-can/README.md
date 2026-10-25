# Gnimu-CAN

Boards for Gnimu-CAN, a work-in-progress telemetry reader that takes data from a car's CAN bus through its OBD-II port. The device is a Seeed XIAO ESP32C6 with an Adafruit CAN Pal transceiver, runs from its own battery, and takes only CAN_H, CAN_L and GND from the car.

It's being developed on a 2018 BMW M2 (F87).

> [!NOTE]
> **Work in progress.** Anything here may change.

## Boards

| Board | Folder | Status |
|---|---|---|
| [**OBD-II helper v1.0**](gnimu-can-obd-helper-v1/) | `gnimu-can-obd-helper-v1/` | Ordered from OSH Park 2026-10-24. Not yet tested. |
| **CAN carrier (XIAO) v1** | [`gnimu-can-carrier-xiao-v1/`](gnimu-can-carrier-xiao-v1/) | Schematic complete. PCB not laid out. |

### OBD-II helper

A small board that fits over the exposed pins on the back of a straight-pin OBD-II plug and breaks out CAN_H, CAN_L and GND to a 3-pin JST PH socket, and 12V and GND to an optional 2-pin one, with a TVS diode on the CAN lines. See [its README](gnimu-can-obd-helper-v1/).

### CAN carrier (XIAO)

A carrier in the same style as the [Gnimu carrier](../gnimu/): off-the-shelf modules on a simple two-layer board. The XIAO runs from a 3.7V LiPo, and the CAN Pal runs from the XIAO's 3V3 output.

| Ref | Part | Role |
|---|---|---|
| **U1** | Seeed XIAO ESP32C6 | MCU, by its 14 edge pins. |
| **U2** | Adafruit CAN Pal | CAN transceiver breakout, powered from the XIAO's 3V3. |
| **R1** | 10kΩ | Pulls `CAN_SILENT` up, so the transceiver stays in listen-only mode until the firmware drives it low. |
| **R2, R3** | 100kΩ + 100kΩ | Battery voltage divider to D0/A0. |
| **C1** | 100nF | Steadies the divider tap for the ADC. |
| **J1** | JST PH 3-pin, side-entry | CAN from the OBD-II helper: 1 CAN_H · 2 GND · 3 CAN_L, the same order as the helper's J3. |
| **J2** | JST PH 2-pin, side-entry | Power switch. |
| **J3** | JST PH 2-pin, side-entry | LiPo battery. |
| **J4** | JST PH 2-pin, side-entry | Leads to the XIAO's battery pads. |

| XIAO pin | Net | Goes to |
|---|---|---|
| D0/A0 | `VBAT_SENSE` | Battery divider tap |
| D8 | `CAN_SILENT` | CAN Pal S (high = listen-only) |
| D9 | `CAN_TX` | CAN Pal TX |
| D10 | `CAN_RX` | CAN Pal RX |

The battery runs through the switch to `VSYS`, which feeds the XIAO's battery pads and the divider, so the switch turns off everything, divider included. The CAN Pal's own termination switch is left off: the car's bus is already terminated.

The symbols and footprints for both boards are in the shared [`libraries/`](../../libraries/) folder.
