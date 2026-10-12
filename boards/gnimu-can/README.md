# Gnimu CAN carrier (XIAO)

> [!NOTE]
> **Work in progress.** This board has a first schematic and no layout. It has not been fabricated, and anything here may change.

A through-hole carrier that puts a XIAO on a CAN bus, in the same style as the [Gnimu carrier](../gnimu/): off-the-shelf modules soldered onto a simple two-layer board.

## Revisions

| Revision | Folder | Status |
|---|---|---|
| **v1** | [`gnimu-can-xiao-v1/`](gnimu-can-xiao-v1/) | Schematic started. PCB not laid out. |

## Modules on the schematic

| Ref | Part | Role |
|---|---|---|
| **U1** | Seeed XIAO ESP32C6 | MCU, by its 14 edge pins. |
| **U2** | XL63070 (TPS63070) buck-boost module | Power regulation. |
| **U3** | Adafruit CAN Pal | CAN transceiver breakout. |

The symbols and footprints for all three are in the shared [`libraries/`](../../libraries/) folder.
