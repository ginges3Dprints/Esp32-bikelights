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
4. You do not need to edit the code. The LED counts, power limit, WiFi, Bluetooth name and update password are all set from the phone dashboard (see **Settings** below). The code only holds first-time defaults (6 LEDs per light, 1500 mA, update password `bike1234`).
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
  - It then **calibrates**: keep the bike upright and still for 3 seconds. Tap **Recalibrate** whenever the phone moves in its holder.
  - Keep the dashboard open **with the screen on**. On iPhone set Settings, then Display & Brightness, then Auto-Lock to **Never** while riding.
  - A live line shows the turn rate, how much you have turned in the look-back window and how much is needed, and the sensor rate. If the sensor rate is 0, the phone has paused the sensors.
  - Use the **Sensitivity** slider if it comes on too easily or not at all. It goes down to 5 degrees. Very high sensitivity can trigger on bumps.
  - If it says left when you turn right, tap **Left/right swapped**.
  - **Show last turns** lists your last 5 turns: peak turn rate, how far you turned, what angle was needed, and whether the indicator came on.
  - **Indicator tuning (advanced)** has sliders for the look-back window, how long it stays on, how straight you must be before it switches off, the calibration time and more. They are saved on your phone and stay when you update the dashboard. **Reset tuning** puts them back to the defaults, and **Use calibration** can be turned off to use live gravity instead.
  - **Copy a report** copies your settings, the last turns, the highest turn rate seen and the sensor status, so you can paste it into a chat or an issue when asking for help.
- **Settings:** see below.
- **Update firmware:** see below.
- **Presets:** Commuter, Be seen, Party, Chill.
- **Battery percentage** shows at the top (or "USB power" if there is no battery).

## Settings (saved on the lights)

The **Settings** card changes things that used to be in the code. They are saved on the ESP32 and **stay when you update the code**, with a cable or wirelessly.

- **Front / Back light: number of LEDs** (1 to 60).
- **Power limit** in mA. Keep it below what your 5V boost converter can supply. Lower it if the ESP32 restarts when the lights are bright.
- **Fix the battery reading:** measure the battery with a multimeter, type the voltage in, and tap the button. The percentage then matches.
- **Wired turn sensor direction** (only shows if you fitted the optional sensor).
- **Bluetooth name** (tap **Restart the lights** afterwards, then connect again).
- **Wireless update password.**
- **Restart the lights.**

Also saved: colours, modes, brightness, indicator layout, auto indicator settings and your WiFi.

Your settings are only lost if you choose "Erase all flash" in the Arduino IDE, or change the partition scheme when uploading with the cable. Pin numbers (GPIO 16, 17, 34) can only be changed in the code.

## Update the code without a cable

1. **First time only:** in the dashboard's **Update firmware** card, type your home WiFi name and password and tap **Save WiFi to the lights**. They are saved on the ESP32.
2. Park the bike and tap **Start update mode**. The ESP32 joins your home WiFi for up to 10 minutes.
3. In the Arduino IDE, choose the network port **bikelights** and upload as normal. Or open the web address shown on the dashboard on a computer on the same WiFi and upload a file made with Sketch, then Export Compiled Binary.

The password is the **Wireless update password** from Settings (`bike1234` until you change it).

## Troubleshooting

- **"Sketch too big" when uploading:** in the Arduino IDE open the **Tools** menu, then **Partition Scheme**, and choose **Minimal SPIFFS (1.9MB APP with OTA/190KB SPIFFS)**. The yellow `#pragma message` lines in the output are harmless notes, not errors.
- **BikeLights does not show up:** check the ESP32 has power, Bluetooth is on, and you are using Bluefy (iPhone) or Chrome (Android).
- **The dashboard looks old:** close it completely and open it again.
- **Left and right are on the wrong sides:** use **Indicator layout**.
- **Battery percentage looks wrong:** use **Fix the battery reading** in Settings.
- **Update mode cannot join WiFi:** save the WiFi name and password again in the dashboard. It must be a 2.4 GHz network. **Forget saved WiFi** clears it.
- **ESP32 restarts when the lights are bright:** lower the **Power limit** in Settings.

## Safety and disclaimer

**Please read before you build or ride.**

- This project is shared **as is**, with **no warranty** of any kind. You build, wire, charge and use it **at your own risk**.
- I am **not responsible** for any injury, accident, fire, damage or loss that results from building or using it, including any problem with the 3D model, the code, the wiring or the batteries.
- It is **not a certified bike light** and has not been tested to any lighting standard. Do not rely on it as your only lights, and check the lighting rules where you live.
- The **auto indicators are experimental**. Always check your surroundings and use hand signals as normal.
- **Batteries can be dangerous.** Use a battery with a protection board, charge it with a proper charger, keep it away from heat, and do not puncture or crush it. Stop using it if it swells or gets hot.
- Check your wiring and the boost converter's 5V output with a multimeter before connecting the ESP32 or the lights.
- The parts are **not waterproof** as printed.
