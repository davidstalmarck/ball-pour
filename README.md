# Ball Pour

Two Atech 14-port boards become two glasses holding 27 rainbow balls, one ball per LED.
Tip a glass and the balls pour out of its rim, one at a time, and land in the other glass
over WiFi (ESP-NOW: board to board, no router, no laptop needed). It works both ways, and
there are always exactly 27 balls.

The whole project is one file: [`project.yaml`](project.yaml), built with the Atech SDK.

## Hardware (per board)

| Port   | Module                         |
|--------|--------------------------------|
| 1–6    | 3x3 light grid (`neopixel`). Port 1 is the rim, port 6 the bottom |
| 7      | Motion sensor (`icm40608` IMU) |
| 8      | USB-C (power / programming)    |
| 9+10   | Screen (`st7735_tft`)          |
| 11     | Button                         |
| 13+14  | Rotary knob                    |

Runs on a powerbank once it's flashed.

## Build and flash

```bash
pip install atech
atech validate .
atech upload . --port <board A's port>
atech upload . --port <board B's port>
atech send balls_role tx --port <board A's port>   # A starts full
atech send balls_role rx --port <board B's port>   # B starts empty
```

`atech ports` lists the boards on USB. Each board remembers its role.

## Using it

- **Screen:** shows how many balls are in this glass and how many the other one has.
  It shows **TX** (transmitter: sending) while this glass pours, and **RX** (receiver:
  catching/listening) otherwise.
- **Button:** short press with the glass standing up (port 6 down) teaches it which way
  is down. Short press with it lying flat sets that as level. Hold 2 s on A to refill.
- **Knob (settings):** turn and press to set:
  - Direction: is the rim at port 1 or port 6? (each glass on its own)
  - Brightness: 10 levels (shared)
  - Colours: rainbow, or 1–4 colours from a palette of 12 (shared)

## Note

The firmware prints status lines over USB. Some versions of the ESP32 Arduino core
block those prints when no laptop is reading. The Atech SDK sets a 5 ms USB TX timeout
to avoid this; use an SDK version that has that fix, or a board running on a powerbank
can freeze after a while.
