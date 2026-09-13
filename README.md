<div align="center">

# luminogotchi

**a robotic desk lamp with tamagotchi-like personality**

[![MIT License](https://img.shields.io/github/license/AdwityaIO/Luminogotchi?style=for-the-badge)](LICENSE)
[![Hack Club Stardance](https://img.shields.io/badge/Hack%20Club-Stardance%202026-ec3750?style=for-the-badge)](https://stardance.hackclub.com)
[![ESP32](https://img.shields.io/badge/ESP32-Arduino-blue?style=for-the-badge&logo=arduino)](https://www.arduino.cc)

[View 3D Demo](https://adwityaio.github.io/Luminogotchi) · [Stardance Project](https://stardance.hackclub.com/projects/11607) · [Report Bug](https://github.com/AdwityaIO/Luminogotchi/issues/new)

</div>

---

## what is this

luminogotchi is a robotic desk lamp inspired by jacob jacobsen's original luxo l-1 desk lamp and pixar's luxo sr. it's a 60cm+ servo-driven arm that expresses personality through motion — eight hardcoded mood states triggered by touch, with no screen, no AI, no cloud dependency.

the concept is simple: instead of a tamagotchi on a screen, the lamp itself is the pet. you tap the aluminium lampshade, it reacts. you hold it, it reacts differently. leave it alone long enough and it gets sad. every animation is built from disney's 12 animation principles — the same techniques pixar used to make a desk lamp feel alive in 1986.

everything runs offline on an ESP32. v1 is entirely hardcoded state machines — deterministic, fast, no internet needed.

---

## current state

this is an active work-in-progress submitted to hack club stardance 2026. here's exactly where things stand:

### done
- `motion.h` / `motion.cpp` — servo control, 4 easing curves, 8 named poses
- `touch.h` / `touch.cpp` — capacitive touch classification (tap / hold / pet gesture)
- interactive 3D demo website (`index.html`) — Three.js, all 8 mood states, metallic shaders
- `BOM.csv` — full bill of materials with pricing (~$138 USD)
- `docs/wiring.md` — complete pin map and power architecture
- `docs/assembly.md` — physical build guide and parallelogram linkage explanation

### in progress
- `mood.h` / `mood.cpp` — the personality state machine that ties touch events to animations
- `Luminogotchi.ino` — main sketch that ties all modules together
- CAD design in Onshape — joint blocks, base foot, lampshade DXF template (have hand sketches, moving to Onshape)

### not started yet
- physical assembly (waiting on CAD to finalise before ordering from PCBWay)
- build photos and demo video
- touch calibration on real hardware

---

## how it works

### arm structure

four MG996R servos drive a 60cm+ articulated arm. each segment uses two parallel steel rods — a parallelogram linkage, same mechanism as the original luxo l-1. this gives lateral rigidity under servo torque that a single rod per segment can't provide.

```
[HEAD]        ← servo 4: head tilt
  │
[MID JOINT]   ← servo 3: mid arm pivot
  │
[LOWER JOINT] ← servo 2: lower arm pivot
  │
[BASE]        ← servo 1: base rotation
```

### personality engine

moods are implemented as named pose targets + easing curves. the same physical pose with different easing feels completely different:

- `easeOut` — snappy, alert, alive
- `easeInOut` — calm, smooth, deliberate  
- `easeIn` — heavy, reluctant, sad
- `linear` — robotic, mechanical (used only for boot sequence)

the mood engine (in progress) will track energy and attention as decaying values — so the lamp gradually gets sleepy or lonely without interaction, not just when a timer fires.

### touch sensing

ESP32 has built-in capacitive touch pins — no extra hardware needed. the aluminium lampshade is wired directly to GPIO 4. copper foil tape on the base and arm connects to GPIO 33 and 32.

three interaction types are classified:
- **tap** — quick touch and release (< 600ms)
- **hold** — sustained contact (> 600ms)
- **pet** — 3+ quick taps within 500ms of each other

---

## hardware

| component | detail |
|---|---|
| ESP32 DevKit V1 38-pin | main microcontroller |
| 4× MG996R metal gear servo | 11kg/cm — one per joint |
| 4× 4mm steel rods 30cm | 2 per segment — parallelogram linkage |
| aluminium sheet 1mm | lampshade + base plate |
| copper foil tape 6mm | capacitive touch zones |
| warm white LED strip 12V | inside shade |
| 5V 5A PSU | dedicated servo power |
| buck converter 12V→5V 3A | powers ESP32 from same rail |
| SLA resin joint blocks (PCBWay) | servo housing — print ×4 |
| FDM base foot (PCBWay) | weighted base |
| sheet metal shade (PCBWay) | 1mm aluminium, cut from DXF |

full list with quantities, unit prices, and suppliers: [BOM.csv](BOM.csv)

estimated total: **~$101 USD**

---

## wiring

full wiring guide: [docs/wiring.md](docs/wiring.md)

| function | gpio |
|---|---|
| servo — base | GPIO 12 |
| servo — lower arm | GPIO 13 |
| servo — mid arm | GPIO 14 |
| servo — head | GPIO 15 |
| touch — shade | GPIO 4 (T0) |
| touch — base | GPIO 33 (T8) |
| touch — arm | GPIO 32 (T9) |

> servo power runs from a dedicated 5V 5A PSU, not USB. common ground between PSU and ESP32 is required — without it PWM signals float and servos twitch.

---

## getting started

### prerequisites

- arduino IDE with ESP32 board package
- `ESP32Servo` library — Tools → Manage Libraries → search ESP32Servo

### setup

```sh
git clone https://github.com/AdwityaIO/Luminogotchi.git
cp firmware/Luminogotchi/secrets.example.h firmware/Luminogotchi/secrets.h
# fill in your wifi credentials in secrets.h
```

open `firmware/Luminogotchi/Luminogotchi.ino`, select board **ESP32 Dev Module**, upload.

### touch calibration

on first boot, open serial monitor at 115200 baud. `initTouch()` prints raw sensor values for all three zones. untouched values should read 60–80. adjust `touchThreshold` in `touch.h` to sit above your resting readings. the aluminium shade reads higher than the foil strips because of its larger surface area.

### servo limits

`baseMin`, `baseMax` etc. in `motion.h` are starting points based on a 60cm arm. retune after assembly — upper joint can bind depending on how the brackets sit.

---

## mood states

| mood | trigger | what it does |
|---|---|---|
| neutral | default | resting position |
| happy | tap on shade | arm rises, head tilts up, brighter |
| sad | long idle | arm droops slowly, light fades |
| alert | hold on base | snaps upright, max brightness |
| sleepy | extended idle | head droops fully, very dim |
| excited | pet gesture (3+ taps) | full stretch, maximum brightness |
| look left | arm touch tap | head turns left — body stays still |
| look right | arm touch second tap | head turns right — body stays still |

---

## project structure

```
Luminogotchi/
├── firmware/Luminogotchi/
│   ├── Luminogotchi.ino        main sketch (in progress)
│   ├── motion.h / motion.cpp   servo control, easing, named poses ✓
│   ├── touch.h / touch.cpp     capacitive touch classification ✓
│   ├── mood.h / mood.cpp       personality state machine (in progress)
│   └── secrets.example.h       wifi config template ✓
├── cad/                        onshape exports coming soon
├── docs/
│   ├── wiring.md               ✓
│   ├── assembly.md             ✓
│   └── images/                 sketches and build photos coming
├── BOM.csv                     ✓
└── index.html                  3D demo site ✓
```

---

## design decisions

**parallelogram rod linkage** — two rods per segment mirrors the original luxo l-1 mechanism. a single rod twists under servo torque at this arm length.

**hardcoded moods, no AI** — fully offline, zero latency, completely deterministic. v2 will add a mediapipe gesture layer via camera but v1 is designed to be a standalone unit that works out of the box.

**metal shade as touch sensor** — ESP32 capacitive touch works with any conductive surface. wiring the shade directly avoids a separate touch IC and makes the interaction feel natural — you touch the lamp, not a hidden button.

**PCBWay for fabrication** — local 3D print services in india have inconsistent tolerances for tight-fit mechanical parts. PCBWay SLA resin gives the accuracy needed for servo slot fits.

---

## roadmap

- [x] motion engine — servo control, easing curves, named poses
- [x] touch handler — tap / hold / pet classification
- [x] 3D demo website
- [x] BOM, wiring, assembly docs
- [ ] mood engine — personality state machine
- [ ] main sketch
- [ ] CAD files — joint block, base, shade DXF
- [ ] physical build + calibration
- [ ] build photos + demo video
- [ ] v2: mediapipe gesture recognition
- [ ] v2: spring-assisted joints

---

## license

MIT — see [LICENSE](LICENSE)

---

## acknowledgments

- jacob jacobsen / luxo ASA — the original 1937 lamp
- pixar's luxo sr. — the 1986 short that proved a lamp could have a soul
- disney's 12 principles of animation — the theory behind the personality system
- hack club stardance — for making projects like this fundable
- [ESP32Servo](https://github.com/madhephaestus/ESP32Servo) by Kevin Harrington
