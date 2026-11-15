# Bike Light System (ESP32)

Smart bike lights you can print and control from your phone. A **hub** holds the ESP32 and the battery, and a **front light** and **back light** (WS2812B LED strip) plug into it. Control colours, modes and indicators over Bluetooth, with no WiFi to join.

**3D model (customizable, print it here):** [MakerWorld page](PASTE-MAKERWORLD-LINK-HERE)

> Not waterproof as printed, and not a certified bike light. Check your local rules for bike lighting.

## What is in this repo

| File | What it is |
|---|---|
| `index.html` | The phone dashboard (opens from GitHub Pages) |
| `bike_lights_ble/bike_lights_ble.ino` | The ESP32 code |
| `bike_light_system.scad` | The customizable 3D model (OpenSCAD) |

## What you need

- ESP32 30-pin dev board (DevKit V1 type, USB-C or micro USB)
- WS2812B LED strip, 5V (6 LEDs per light by default)
- Power, either:
  - a USB cable, or
  - a LiPo battery **with a protection board**, a TP4056 charger board and a **5V** boost converter (or one IP5306 5V power bank board)
- Two 100 kΩ resistors and a 100 nF capacitor (battery percentage)
- Two 330 Ω resistors (one in each LED data wire, recommended)
- A slide switch, thin wire, zip ties

## Wiring

| From | To |
|---|---|
| Front strip data | ESP32 GPIO 16 (through a 330 Ω resistor) |
| Back strip data | ESP32 GPIO 17 (through a 330 Ω resistor) |
| 5V from the boost converter | Both strips' 5V and the ESP32 5V pin |
| Grounds | All joined together |
| Battery + | 100 kΩ resistor, then GPIO 34, then another 100 kΩ resistor to GND (100 nF capacitor from GPIO 34 to GND) |

Check the boost converter's output with a multimeter (about 5V) **before** connecting the ESP32 or the lights.

Optional: an MPU6050 sensor (SDA to GPIO 21, SCL to GPIO 22, 3.3V, GND) can detect turns. You do not need it, because the phone can do this job.

## Upload the ESP32 code (first time, with a cable)

1. Install the Arduino IDE and add ESP32 board support.
2. Install the **FastLED** library (Library Manager).
3. Open `bike_lights_ble.ino`.
4. Near the top, check `FRONT_LEDS` and `BACK_LEDS` match your lights. If you want wireless updates, also set `WIFI_SSID`, `WIFI_PASS` and change `OTA_PASS`.
5. Board: **ESP32 Dev Module**. Partition Scheme: **Minimal SPIFFS (1.9MB APP with OTA)**.
6. Upload.

## Use it on your phone

Open the dashboard: **https://ginges3dprints.github.io/Esp32-bikelights/**

- **iPhone:** use the free **Bluefy** browser. Safari cannot do Bluetooth.
- **Android:** use **Chrome**.

Tap **Connect to BikeLights** and choose BikeLights. Bookmark the page.

iPhone alternative: the free **Bluefruit Connect** app (Controller screen) gives a colour picker and buttons, with fewer features.

## The dashboard

- **Which light?** Front, Back or Both.
- **Colour, Brightness, Speed.** Colour buttons for quick picks.
- **Mode:** Solid, Breathe, Flash, Rainbow, Colour fade, Chase, Scanner, Twinkle, Fire, Red-Blue.
- **Indicators:** Left, Hazard, Right. They stop after 20 seconds.
- **Indicator layout:** if left and right flash the wrong LEDs, tap the Front or Back button to swap sides. Tap Left or Right to test.
- **Auto indicators** (experimental): your phone's motion sensors turn the indicator on when the bike turns and off when you are straight again.
  - Tap **Detect turns with this phone** and allow motion access.
  - Keep the dashboard open with the screen on.
  - Use the **Sensitivity** slider if it comes on too easily or not at all.
  - If it says left when you turn right, tap **Left/right swapped**.
  - **Show last turn details** displays the peak turn rate of your last turn, which helps with tuning.
- **Update firmware:** see below.
- **Presets:** Commuter, Be seen, Party, Chill.
- **Battery percentage** shows at the top (or "USB power" if there is no battery).

## Update the code without a cable

1. On the dashboard, tap **Start update mode** (park the bike first).
2. The ESP32 joins your home WiFi for up to 10 minutes.
3. In the Arduino IDE, choose the network port **bikelights** and upload as normal. Or open the web address shown on the dashboard on a computer on the same WiFi and upload a file made with Sketch, then Export Compiled Binary.

The password is `OTA_PASS` from the code.

## Troubleshooting

- **BikeLights does not show up:** check the ESP32 has power, Bluetooth is on, and you are using Bluefy (iPhone) or Chrome (Android).
- **The dashboard looks old:** close it completely and open it again.
- **Left and right are on the wrong sides:** use **Indicator layout**.
- **Battery percentage looks wrong:** change `BATT_CAL` in the code a little (for example 1.02).
- **Update mode cannot join WiFi:** check the WiFi name and password in the code. It must be a 2.4 GHz network.
- **ESP32 restarts when the lights are bright:** lower `MAX_MILLIAMPS` in the code.

## Safety

Use a battery with a protection board, keep it away from heat, and do not puncture it. Do not ride in a way that depends on these lights alone being legal where you live.
