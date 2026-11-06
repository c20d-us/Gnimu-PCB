# Gnimu-CAN

Boards for Gnimu-CAN, a work-in-progress telemetry reader that takes data from a car's CAN bus through its OBD-II port, or directly from the CAN bus's CAN-H/CAN-L. The device uses a Seeed XIAO ESP32C6 MCU with an Adafruit CAN Pal transceiver. It runs either from its own LiPo battery or from the car's 12V supply, depending on which carrier it's built on.

> [!NOTE]
> **Work in progress.** Anything here may change. No boards other than the OBD-II Helper board have been sent to a fab.

## Boards

| Board | Folder | Status |
|---|---|---|
| [**OBD-II helper v1.0**](gnimu-can-obd-helper-v1.0/) | `gnimu-can-obd-helper-v1.0/` | Ordered from OSH Park 2026-10-24. J2's +12V and GND pins are reversed relative to stock red/black leads; see its README. |
| [**OBD-II helper v1.1**](gnimu-can-obd-helper-v1.1/) | `gnimu-can-obd-helper-v1.1/` | Layout complete. Not yet ordered. J2 swapped to pin 1 GND, pin 2 +12V; ground pour on both layers. |
| **CAN carrier (XIAO), LiPo v1.0** | [`gnimu-can-carrier-xiao-lipo-v1.0/`](gnimu-can-carrier-xiao-lipo-v1.0/) | Layout complete and passing DRC. Not yet ordered. |
| **CAN carrier (XIAO), 12V v1.0** | [`gnimu-can-carrier-xiao-12v-v1.0/`](gnimu-can-carrier-xiao-12v-v1.0/) | Layout complete and passing DRC. Not yet ordered. |

### OBD-II helper

A small board that fits over the exposed pins on the back of a straight-pin OBD-II plug and breaks out CAN_H, CAN_L and GND to a 3-pin JST PH socket, and 12V and GND to an optional 2-pin one, with a TVS diode on the CAN lines. See [its README](gnimu-can-obd-helper-v1.0/).

### CAN carriers (XIAO)

Two carriers in the same style as the [Gnimu carrier](../gnimu/): off-the-shelf modules on a simple two-layer, through-hole board. They share the same CAN section and differ only in how they're powered:

- **LiPo:** the XIAO runs from a 3.7V LiPo through its battery pads. Only CAN_H, CAN_L and GND come from the car.
- **12V:** the XIAO runs from the car's 12V, through a fuse, a TVS diode and a 5V buck regulator. It takes CAN_H, CAN_L, GND and +12V from the helper's two sockets.

All the off-board connections are plated holes for soldering leads, typically JST PH pigtails that plug into the helper. Unplated strain-relief holes (SR1, SR2…) sit beside the pads. The four corner holes are for M2.5 standoffs.

#### Shared CAN section

| Ref | Part | Role |
|---|---|---|
| **U1** | Seeed XIAO ESP32C6 | MCU, by its 14 edge pins. |
| **U2** | Adafruit CAN Pal | CAN transceiver breakout, powered from the XIAO's 3V3. |
| **R1** | 10kΩ | Pulls `CAN_SILENT` up, so the transceiver stays in listen-only mode until the firmware drives it low. |
| **J1** | 3 wire pads | CAN from the OBD-II helper: 1 CAN_H · 2 GND · 3 CAN_L, the same order as the helper's J3. |

| XIAO pin | Net | Goes to |
|---|---|---|
| D0/A0 | `VBAT_SENSE` (LiPo) or `V12_SENSE` (12V) | Supply voltage divider tap |
| D8 | `CAN_SILENT` | CAN Pal S (high = listen-only) |
| D9 | `CAN_TX` | CAN Pal TX |
| D10 | `CAN_RX` | CAN Pal RX |

The CAN Pal's own termination switch is left off: the car's bus is already terminated.

#### LiPo carrier

48 × 44 mm. All parts are through-hole.

| Ref | Part | Role |
|---|---|---|
| **R2, R3** | 100kΩ + 100kΩ | Battery voltage divider to D0/A0 (÷2). |
| **C1** | 100nF | Steadies the divider tap for the ADC. |
| **J2** | 2 wire pads | Power switch. |
| **J3** | 2 wire pads | LiPo battery. |
| **J4** | 2 wire pads | Leads to the XIAO's battery pads. |

The battery runs through the switch to `VSYS`, which feeds the XIAO's battery pads and the divider, so the switch turns off everything, divider included.

The XIAO's built-in charger works through those same battery pads, so the LiPo charges over USB only while the switch is on. With the switch off, the battery is fully disconnected and nothing drains it.

#### 12V carrier

56 × 44 mm. All parts are through-hole.

| Ref | Part | Role |
|---|---|---|
| **J2** | 2 wire pads | 12V in from the helper's J2. |
| **F1** | Bourns MF-R050 | Resettable PTC fuse: 0.5A hold, 1A trip, 60V. |
| **D1** | P6KE18A | Unidirectional TVS diode: 15.3V standoff, about 25V clamp. Cathode to the fused 12V. |
| **D2** | 1N5819 | Series Schottky diode after D1, for reverse-polarity protection. |
| **J3** | 2 wire pads | Power switch: **Common** (protected 12V) and **On** (switched 12V). |
| **C1** | 47µF / 35V electrolytic (Rubycon 35YXJ47M5X11) | Input bulk capacitor, close to U3's VIN as Pololu recommends. |
| **U3** | Pololu D24V7F5 | 5V 600mA step-down regulator, 5.1–36V in. |
| **D3** | 1N5819 | Schottky diode from U3's output to the XIAO's 5V (VBUS) pin. |
| **R2, R3** | 100kΩ + 10kΩ | Divider from the switched 12V to D0/A0 (÷11). |
| **C2** | 100nF | Steadies the divider tap for the ADC. |

The power path is:

```
J2 +12V → F1 → D1 (to GND) → D2 → J3 switch → C1 → U3 → D3 → XIAO 5V pin
```

- **Reverse polarity.** The D24V7F5 has no reverse-voltage protection of its own. If 12V is connected backwards, D1 conducts forward and F1 trips, while D2 blocks the negative voltage so the regulator, C1 and the divider never see it. F1 resets once the fault is removed.
- **Overvoltage.** If the input rises well above about 17V, D1 breaks down and clamps it, and a sustained overvoltage trips F1. D1 also clamps negative spikes before they reach D2.
- **D3** keeps the XIAO's USB 5V from feeding back into the regulator when a USB cable is plugged in. Seeed recommends a diode like this whenever the 5V pin is powered externally.
- **The switch** sits after the fuse and TVS, so it turns off the regulator, the XIAO and the divider together. To build without a switch (like if you get +12V from a switched source rather than the OBD-II port), bridge J3 with a wire link, or fit a 2-pin header and a jumper.
- **The divider** gives 1.35V at 14.8V and 3.27V at 36V, so the ADC covers the regulator's whole input range. Multiply the reading at D0 by 11, then add about 0.35V for D2's drop, to get the supply voltage.
- **Antenna.** The XIAO's antenna is at the end opposite its USB port. A keep-out zone on both copper layers keeps tracks and the ground pour away from it.

The symbols, footprints and 3D models for all the boards are in the shared [`libraries/`](../../libraries/) folder.
