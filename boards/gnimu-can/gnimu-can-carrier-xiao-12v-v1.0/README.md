# Gnimu-CAN carrier, 12V (v1.0)

A two-layer, through-hole carrier for the Gnimu-CAN telemetry reader. It holds a Seeed XIAO ESP32C6 and an Adafruit CAN Pal transceiver, and runs from the car's 12V supply through a fuse, protection diodes and a 5V buck regulator. CAN and 12V come from the [OBD-II helper](../gnimu-can-obd-helper-v1.1/)'s two sockets.

For a version that runs from its own LiPo and takes only CAN from the car, see the [LiPo carrier](../gnimu-can-carrier-xiao-lipo-v1.0/).

> [!NOTE]
> **Ordered, not yet received.** v1.0 was ordered from OSH Park. Assembly notes will be added once boards arrive.

---

## The board

| | |
|---|---|
| Size | 56 × 44 mm |
| Layers | 2, 1.6 mm, with a ground pour on both |
| Parts | All through-hole. The XIAO, CAN Pal and regulator mount on pin headers. |
| Mounting | Four corner holes for M2.5 standoffs |

The XIAO's USB-C port overhangs the left edge so a cable can reach it. Its antenna is at the other end, and a keep-out zone on both copper layers keeps tracks and the ground pour away from it.

### Connections

All the off-board connections are plated holes for soldering leads, typically JST PH pigtails that plug into the helper. Unplated strain-relief holes sit beside them.

| Ref | Pads | Silkscreen | Use |
|---|---|---|---|
| **J1** | 1 CAN_H · 2 GND · 3 CAN_L | `CANH GND CANL` | CAN from the helper's J3, in the same order. |
| **J2** | 1 +12V · 2 GND | `+`, `-` | 12V from the helper's J2. |
| **J3** | 1 On · 2 Common | `On`, `Common` | Power switch. To build without one, bridge J3 with a wire link, or fit a 2-pin header and a jumper. |

The helper v1.1's J2 has GND on pin 1, but these are solder pads, so only the wire colours matter: red to `+`, black to `-`.

### XIAO pins

| XIAO pin | Net | Goes to |
|---|---|---|
| D0/A0 | `V12_SENSE` | Supply voltage divider tap |
| D8 | `CAN_SILENT` | CAN Pal S (high = listen-only) |
| D9 | `CAN_TX` | CAN Pal TX |
| D10 | `CAN_RX` | CAN Pal RX |
| 3V3 | `+3V3` | CAN Pal VCC, R1 |
| 5V | `VBUS` | D3's cathode, from the regulator |

### Power path

```
J2 +12V → F1 → D1 (to GND) → D2 → J3 switch → C1 → U3 → D3 → XIAO 5V pin
```

- **Reverse polarity.** The D24V7F5 has no reverse-voltage protection of its own. If 12V is connected backwards, D1 conducts forward and F1 trips, while D2 blocks the negative voltage so the regulator, C1 and the divider never see it. F1 resets once the fault is removed.
- **Overvoltage.** If the input rises well above about 17V, D1 breaks down and clamps it, and a sustained overvoltage trips F1. D1 also clamps negative spikes before they reach D2.
- **USB.** D3 keeps the XIAO's USB 5V from feeding back into the regulator when a USB cable is plugged in. Seeed recommends a diode like this whenever the 5V pin is powered externally.
- **The switch** sits after the fuse and protection, so it turns off the regulator, the XIAO and the divider together.
- **The divider** (R2, R3) gives 1.35V at 14.8V and 3.27V at 36V, so the ADC covers the regulator's whole input range. Multiply the reading at D0 by 11, then add about 0.35V for D2's drop, to get the supply voltage.

R1 pulls `CAN_SILENT` high, so the transceiver stays in listen-only mode until the firmware drives it low. The CAN Pal's own termination switch is left off: the car's bus is already terminated.

---

## Parts

| Ref | Part | Notes |
|---|---|---|
| U1 | Seeed XIAO ESP32C6 | On 2× 1×7 pin headers. |
| U2 | Adafruit CAN Pal (5708) | On its 1×7 header. The screw terminal isn't needed; CAN comes through the header. |
| U3 | Pololu D24V7F5 (5593) | 5V 600mA step-down, 5.1–36V in. On its 1×3 header. |
| F1 | Bourns MF-R050 | Resettable PTC fuse: 0.5A hold, 1A trip, 60V. 5 mm lead spacing. |
| D1 | P6KE18A | Unidirectional TVS diode, DO-15: 15.3V standoff, about 25V clamp. |
| D2, D3 | 1N5819 | 40V 1A Schottky diodes, DO-41. |
| C1 | Rubycon 35YXJ47M5X11 | 47µF 35V electrolytic, 5 mm diameter, 2 mm lead spacing. |
| C2 | 100nF ceramic | Disc, 2.5 mm lead spacing. |
| R1, R3 | 10kΩ | Axial, 10.16 mm (0.4") lead spacing. |
| R2 | 100kΩ | Axial, 10.16 mm (0.4") lead spacing. |

### Assembly notes

- **Diode bands:** each diode's band goes over the band on the silkscreen, on the square pad marked `K`.
  - D1's cathode goes to the fused 12V.
  - D2's cathode faces J3.
  - D3's cathode faces the XIAO.
- **C1** is polarised: its `+` lead goes in the square pad marked `+`.
- **Ground pads:** square pads are ground. The only exceptions are the polarity marks on C1's `+` pad and the diodes' cathode pads.

---

## Design notes

- Power traces are 0.5 mm and signal traces 0.3 mm.
- The ground pads are square with 45° thermal spokes. These are set on this board's copies of the footprints, not in the libraries, so DRC reports six footprints that differ from their library versions. That's expected. **Update Footprint from Library** would undo them.
- The symbols, footprints and 3D models are in the shared [`libraries/`](../../../libraries/) folder.

---

## Fabrication

The KiCad project is the source. [`fabrication/`](fabrication/) holds gerbers that capture the design as sent.

v1.0 was ordered from OSH Park's standard 2-layer service: 1.6 mm board, 1 oz copper. Three boards cost $19.05 shipped.

> [!IMPORTANT]
> OSH Park does not refill KiCad zones. If you upload the `.kicad_pcb`, refill the zones (press **B** in the PCB editor) and save first, or the ground pour may be stale.
