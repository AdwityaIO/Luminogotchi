# wiring guide

## power architecture

Two separate power rails sharing a common ground. Running 4x MG996R from USB or the ESP32's 3.3V pin will brownout and cause erratic servo behavior.

```
Wall Outlet
    │
    └── 5V 5A PSU ──────────────────────── Servo VCC (red wire, all 4 servos)
          │
          ├── Buck Converter (5V 3A out) ── ESP32 VIN
          │
          └── Common GND ─────────────────── Servo GND (brown wire, all 4 servos)
                                        └─── ESP32 GND
```

**The common ground between ESP32 and servo PSU is not optional.** Without it, PWM signals float and servos twitch randomly.

---

## servo connections

| Servo | Joint | ESP32 Pin | Wire colour (signal) |
|---|---|---|---|
| S1 | Base rotation | GPIO 12 | Yellow/White |
| S2 | Lower arm pivot | GPIO 13 | Yellow/White |
| S3 | Mid arm pivot | GPIO 14 | Yellow/White |
| S4 | Head tilt | GPIO 15 | Yellow/White |

All servo VCC (red) → 5V PSU rail  
All servo GND (brown/black) → PSU GND + ESP32 GND (common)

---

## capacitive touch connections

No extra components needed — ESP32 GPIO pins have built-in capacitive touch sensing. Wire directly from the metal surface to the pin. Longer wires increase sensitivity (also increase noise), keep runs under 30cm where possible.

| Zone | Surface | ESP32 Touch Pin | GPIO |
|---|---|---|---|
| Shade | Aluminium lampshade inner surface | T0 | GPIO 4 |
| Base | Copper foil tape strip on base body | T8 | GPIO 33 |
| Arm | Copper foil tape wrapped around upper rod | T9 | GPIO 32 |

Route touch wires through the hollow steel rods where possible. Wrap exposed sections in heat shrink to prevent shorts against the rod walls.

**Calibration:** on first boot, `initTouch()` prints raw readings to Serial Monitor. Untouched values should be 60–80. Touched values should drop below 40. If untouched reads are already below 50, shorten the wire or lower `touchThreshold` in `touch.h`.

---

## LED strip

| LED strip wire | Connects to |
|---|---|
| 12V positive | 12V rail (or 5V PSU positive — check your strip voltage) |
| GND | Common GND |
| PWM signal (if addressable) | Any available ESP32 GPIO |

For v1, the strip runs at fixed brightness. PWM dimming tied to mood state is planned for v2.

---

## full pin map

| GPIO | Function |
|---|---|
| 12 | Servo S1 — base |
| 13 | Servo S2 — lower arm |
| 14 | Servo S3 — mid arm |
| 15 | Servo S4 — head |
| 4  | Touch — shade (T0) |
| 33 | Touch — base (T8) |
| 32 | Touch — arm (T9) |

Avoid GPIO 6–11 (connected to flash), 34–39 (input only, no PWM).
