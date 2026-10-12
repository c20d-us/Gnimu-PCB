# Gnimu carrier (XIAO)

A small through-hole carrier for the portable, battery-powered [Gnimu](https://github.com/c20d-us/Gnimu) build. A Seeed XIAO nRF52840 Sense and an XL63070 buck-boost module solder onto it, and labelled pads take the leads for everything else. It replaces the point-to-point wiring of a hand-wired build; the connections and the firmware are the same either way.

For the parts list, assembly steps, and usage, follow the [XIAO nRF52840 build guide](https://github.com/c20d-us/Gnimu/blob/main/docs/build-nrf52840.md) in the Gnimu repository.

<img src="../../images/gnimu-carrier-xiao-v1-loaded-pcb.jpeg" alt="The Gnimu carrier PCB with the XIAO, regulator, resistors and leads fitted" width="420">

---

## Revisions

| Revision | Date | Folder | Fabrication outputs |
|---|---|---|---|
| **v1.1** | 2026-10-10 | [`gnimu-carrier-xiao-v1.1/`](gnimu-carrier-xiao-v1.1/) | Not yet added |
| **v1** | 2026-09-27 | [`gnimu-carrier-xiao-v1/`](gnimu-carrier-xiao-v1/) | [`fabrication/`](gnimu-carrier-xiao-v1/fabrication/), gerbers as ordered from JLCPCB |

**v1.1** differs from v1 only in its larger mounting holes (2.7mm for M2.5, up from 2.2mm for M2) and a printed reminder of the regulator's settings, `PS open · 3V3`. The two are wired identically.

---

## The board

- 74.8 × 44.8 mm, two copper layers, through-hole only.
- Six mounting holes, at the corners and the middle of each long edge.
- A pair of tie-down holes beside each of the GNSS, BATT, Switch and XIAO BATT pads, for strain-relieving the leads.
- The Gnimu logo and the revision on both sides.

### Soldered on

| Silkscreen | Part |
|---|---|
| **XIAO nRF52840 Sense** | The XIAO, by its 14 edge pins, with its USB connector at the board edge marked **USB**. |
| **XL63070** | The XL63070 (TPS63070) buck-boost module, the 3.3V version, with its PS solder jumper left open as supplied. |
| **510kΩ** (two positions) | The switch-sense voltage divider. |

### Wire pads

All pads are on a 2.54mm pitch.

| Silkscreen | Pads | Connects to |
|---|---|---|
| **GNSS** | G · V · TX · RX | The GNSS module: ground, 3.3V power, and the module's own TX and RX pins. |
| **BATT** | + · − | The LiPo. |
| **Switch** | ON · COMMON · OFF | The slide switch: its common terminal to COMMON, its two throws to ON and OFF. |
| **XIAO BATT** | + · − | The battery pads on the underside of the XIAO. |
| **I2C** | GND · 3V3 · SDA · SCL | Spare. Not used by the firmware. |
| **GPIO** | GND · D10 · D9 · D8 · D2 · D3 | Spare. Not used by the firmware. |

The **TX** and **RX** pads are named for the GNSS module's pins: wire the module's TX to TX and its RX to RX. The crossover to the XIAO is done on the board.

### How it's wired

| XIAO pin | Goes to | Purpose |
|---|---|---|
| **D6** (Serial1 TX) | GNSS **RX** | UART |
| **D7** (Serial1 RX) | GNSS **TX** | UART |
| **D1** | Regulator **EN** | GNSS power control. The module's own pull-up keeps it on; the XIAO pulls it low to switch the GNSS off. |
| **D0 / A0** | Switch-sense divider tap | Reads about 2V with the switch off and about 0V with it on. |

```
        (+) ─ SW common
               │
  LiPo         ├─[ SW ON  ]──┬──────────────────────► XIAO BAT+
  3.7V         │             └──────────────────────► regulator VIN
               └─[ SW OFF ]──[ 510kΩ ]──┬───────────► XIAO D0/A0
                                        └─[ 510kΩ ]─► GND
        (–) ────────────────────────────────────────► XIAO BAT−, regulator GND, GNSS GND

  regulator VOUT (3.3V) ────────────────────────────► GNSS VCC
```

The regulator powers only the GNSS; the XIAO runs directly from the battery.

### Mounting orientation

The **IMU** axes printed beside the XIAO show how the board must sit in the car: the **X** arrow points to the front of the car and the **Y** arrow to its left, with the component side facing up. That puts the XIAO's USB connector toward the rear.

---

## Before connecting the GNSS

> [!WARNING]
> The regulator module can be supplied set to 9V, which will destroy the GNSS module. Confirm it is the 3.3V version and measure **VOUT** before the GNSS is connected. See the [build guide](https://github.com/c20d-us/Gnimu/blob/main/docs/build-nrf52840.md#check-the-regulator-before-connecting-the-gnss).
