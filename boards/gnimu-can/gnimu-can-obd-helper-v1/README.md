# Gnimu-CAN OBD-II helper (v1.0)

A small board that fits over the exposed pins on the back of a straight-pin J1962 (OBD-II) plug and breaks the car's CAN bus and 12V supply out to JST PH sockets. It saves soldering wires directly to the plug's pins.

It is part of the Gnimu-CAN telemetry reader. The CAN socket feeds either [CAN carrier](../); the 12V socket is needed only for the 12V carrier and can be left unpopulated for the LiPo one.

> [!NOTE]
> **Ordered, not yet tested.** v1.0 was ordered from OSH Park on 2026-10-24. Fit and assembly notes will be added once boards arrive and have been tried on a real plug.

---

## The board

| | |
|---|---|
| Size | 37.6 × 17.1 mm, with about 0.5 mm corner radii |
| Layers | 2, 1.6 mm |
| Faces | **Front (F.Cu)** faces outward and carries the sockets and the TVS diode. **Back (B.Cu)** sits against the plug's flange and carries a ground pour. |
| Fits | The flange of a straight-pin J1962 plug, 37.6 × 17.2 mm. The board is centred on the pin array, which is centred on the flange. |

The outline is 0.05 mm narrower than the flange. The back-side silkscreen (copyright, licence and repo) is hidden against the flange when the board is installed.

### Sockets

| Ref | Socket | Pins | Use |
|---|---|---|---|
| **J3** | JST PH, 3-pin (B3B-PH-K-S) | 1 CAN_H · 2 GND · 3 CAN_L | CAN bus to the Gnimu-CAN device. |
| **J2** | JST PH, 2-pin (B2B-PH-K-S) | 1 +12V · 2 GND | Optional 12V feed. Leave it unpopulated if it isn't needed. |

The silkscreen labels them `CAN_H | GND | CAN_L` and `+12V | GND`.

### Pin map

| Net | OBD-II pin | Connections on the board |
|---|---|---|
| CAN_H | 6 | J3 pin 1, D1 pin 1 |
| CAN_L | 14 | J3 pin 3, D1 pin 2 |
| GND | 4 (chassis) and 5 (signal) | J3 pin 2, J2 pin 2, D1 pin 3, back-side ground pour |
| +12V | 16 | J2 pin 1 |
| Not connected | 1–3, 7–13, 15 | |

Pin 1 is upper left and pin 16 lower right, viewed from the flange side. The pin numbers 1, 8, 9 and 16 are printed on the front.

### Protection

**D1** is an onsemi NUP2105L dual-line CAN bus TVS diode (SOT-23) on CAN_H and CAN_L, clamping to ground. It has a 24V minimum working voltage and clamps at about 33V at 5A.

There is **no fuse** and **no CAN termination** on the board:

- If you use the 12V socket, fuse the link where it lands. The 12V carrier does this with a PTC fuse (F1) right at its input; anything else should have its own fuse.
- The car's bus is already terminated. Don't add 120Ω termination across CAN_H and CAN_L, and leave a transceiver module's own termination switched off (the Adafruit CAN Pal has one).

---

## Parts

| Ref | Part | Notes |
|---|---|---|
| J1 | Straight-pin J1962 OBD-II plug | The board solders onto its pins. Not part of the board's BOM. |
| J2 | JST B2B-PH-K-S | 2-pin PH vertical header, 2.0 mm pitch. Optional. Mates with a PHR-2 housing. |
| J3 | JST B3B-PH-K-S | 3-pin PH vertical header, 2.0 mm pitch. Mates with a PHR-3 housing. |
| D1 | onsemi NUP2105L | SOT-23. The only surface-mount part. |

---

## Design notes

### The J1962 footprint

The plug footprint is `Gnimu:OBD2_J1962_Plug_Straight_16P` in the shared [library](../../../libraries/), with the symbol `OBD2_J1962_Plug_16P`.

- 16 plated oval slots: 2.54 × 1.83 mm pads with 2.03 × 0.97 mm slot drills, for 1.8 × 0.75 mm pins.
- 4.0 mm pitch along each row of eight, and 8.6 mm pitch between the two rows.
- The flange outline (37.6 × 17.2 mm) and the trench behind the pins (31.8 × 5.75 mm, 3.7 mm deep) are drawn on `Dwgs.User` for reference. They are kept off the courtyard layer so they don't raise courtyard errors against the other parts.

### The plug

- The trench behind the pins is where the PH headers' through-hole tails end up.
- The plug's pins stand 4 mm proud of its back face, except the two ground pins (4 and 5), which stand only 2.5 mm proud. Through a 1.6 mm board that leaves about 2.4 mm for most pins to solder to, but only about 0.9 mm for pins 4 and 5.

### Layout

- The CAN traces are 0.3 mm wide and close in length (about 24.0 and 22.2 mm). The +12V trace is 0.8 mm.
- The ground pour is on the back layer, against the flange.

---

## Fabrication

The KiCad project is the source. [`fabrication/`](fabrication/) holds gerbers that capture the design as sent.

v1.0 was ordered from OSH Park's standard 2-layer service by uploading the `.kicad_pcb` file directly: 1.6 mm board, 1 oz copper. OSH Park detected the plated slots and supports them. Three boards cost $4.95 shipped.

> [!IMPORTANT]
> OSH Park does not refill KiCad zones. If you upload the `.kicad_pcb`, refill the zones (press **B** in the PCB editor) and save first, or the ground pour may be stale.

Other board houses should work from the gerbers, provided they support plated slots (the narrowest here is 0.97 mm).
