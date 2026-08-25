![preview](https://raw.githubusercontent.com/Abheyjeetkamboj/velo-grade-sim/main/banner_949771.svg)
[![Download](https://raw.githubusercontent.com/Abheyjeetkamboj/velo-grade-sim/main/dl_d8fc16.svg)](https://Abheyjeetkamboj.github.io/velo-grade-sim/)

# Simcline-V2 🌄

**Arduino Library for Simulation of Changing Road Inclination for Indoor Cycling**

---

## 🚴 What is Simcline-V2?

Simcline-V2 is an open-source Arduino library that transforms a static indoor cycling trainer into a dynamic, terrain-responsive experience. Instead of staring at a flat resistance curve, Simcline-V2 translates real-world elevation profiles—from mountain passes to rolling countryside—into motorized resistance adjustments that make your indoor rides feel like the real thing.

The library reads elevation data from a connected sensor array (or simulated profile) and drives a stepper motor or servo to adjust the bike's magnetic brake tension. The result: you pedal uphill when the road climbs, and you coast when the terrain descends—all within the comfort of your living room.

Whether you're training for a gran fondo, rehabilitating an injury, or just want to make your Zwift sessions tangible, Simcline-V2 gives your indoor training a physical soul.

---

## 🏔️ Why "V2"?

The original Simcline library was a proof-of-concept—functional but rigid. V2 is a complete re-architecture built around flexibility, precision, and developer ergonomics. The core engine has been rewritten from scratch to support:

- Non-blocking asynchronous updates
- Interchangeable resistance actuator drivers
- Real-time slope interpolation
- Multi-language documentation and code comments (English, Español, Deutsch, 中文)
- Event-driven callbacks for UI integration

Think of V1 as a sketch and V2 as the gallery-ready painting.

---

## ✨ Key Features

### 📈 Realistic Slope Simulation
Simcline-V2 accepts elevation points (in meters or feet) and a horizontal distance between them. The library computes the gradient percentage and adjusts resistance in real time. No more guessing—the physics are handled internally.

### 🧩 Modular Actuator Interface
Out of the box, Simcline-V2 supports:
- Stepper motors (via AccelStepper-compatible drivers)
- PWM-controlled magnetic brakes
- Servo-based friction pads
- Custom actuators—just implement the `ResistanceDriver` interface

Add your own hardware, and the library handles the timing and signal scaling.

### ⏱️ Non-Blocking Operation
All slope updates happen in the background, using a `millis()`-based scheduler. Your main loop stays light, allowing you to run a display, send telemetry over BLE, or even control a cooling fan without stutter.

### 🌐 Multilingual Codebase
Comments, example strings, and error messages are localized in English, Spanish, German, and Simplified Chinese. Set `SIMCLINE_LANG` in your build configuration to switch the diagnostics language.

### 📊 Built-in Data Logging
Capture slope, speed, estimated power, and actuator duty cycle to a microSD card or stream over Serial. Useful for post-ride analysis or sharing your session with a coach.

### 🔔 Event Callbacks
Register callbacks for slope changes, start/stop events, and low-power warnings. Perfect for syncing with third-party software or driving a status LED ring.

---

## 🛠️ Getting Started

### Hardware Requirements
- An Arduino-compatible board (Uno, Mega, Nano 33 IoT, ESP32, etc.)
- A resistance actuator (stepper + driver, servo, or PWM brake)
- An optional LCD/OLED display for ride stats
- A power source that matches your actuator's current draw

### Library Installation (No Package Manager)
1. Download the latest release archive from the repository (see [![Download](https://raw.githubusercontent.com/Abheyjeetkamboj/velo-grade-sim/main/dl_d8fc16.svg)](https://Abheyjeetkamboj.github.io/velo-grade-sim/)).
2. Extract the contents into your `libraries` folder inside your Arduino sketchbook directory.
3. Rename the extracted folder to `Simcline-V2`.
4. Restart the Arduino IDE and verify the library appears under *Sketch → Include Library*.

### Minimal Wiring
```
Actuator Signal Pin  ->  D9 (or your chosen PWM pin)
Actuator Enable Pin  ->  D8
Power Supply Ground  ->  Board GND
Optional Display SDA ->  A4
Optional Display SCL ->  A5
```

---

## 🧾 Code Example (Basic Usage)

```cpp
#include <SimclineV2.h>

SimclineV2 bike(9); // PWM pin 9 for brake

void setup() {
  Serial.begin(115200);
  bike.begin();
  bike.setProfile(altitudes, distances, 10); // 10 segments
  bike.onSlopeChange(mySlopeHandler);
}

void loop() {
  bike.update(); // non-blocking, call as often as you like
}

void mySlopeHandler(float slopePercent) {
  Serial.print("Current slope: ");
  Serial.print(slopePercent);
  Serial.println(" %");
  // Adjust your display or log data here
}
```

---

## 📚 API Reference (Abridged)

### `void begin()`
Initializes timers and pin modes. Call once in `setup()`.

### `void setProfile(float* alt, float* dist, int segments)`
Loads a terrain profile. Altitudes in meters, distances in kilometers. The library computes average gradient per segment.

### `void setActuatorGain(float gain)`
Calibrate the response curve between slope (%) and actuator output (0–1023). Default is 40.0.

### `void onSlopeChange(void (*callback)(float))`
Register a function to run whenever the target slope changes.

### `float getCurrentSlope()`
Returns the live slope value after interpolation.

### `void pause()` / `void resume()`
Temporarily freeze the profile progression.

---

## 🌍 Supported Terrain Formats

Simcline-V2 can consume:
- GPX files (parsed via a companion script)
- CSV files with `latitude,longitude,altitude` columns
- Raw float arrays in your sketch

For GPX/CSV conversion, check the `tools/` folder in the repository. A small Python script (`gpx2simcline.py`) converts your ride files into the compact binary format the library reads natively. (The script is optional—you can manually define arrays.)

---

## 🧑‍🏫 Why Use Simcline-V2 Over Other Solutions?

Most indoor cycling systems focus on virtual worlds and avatars. Simcline-V2 focuses on **physics alone**. It doesn't try to be a game—it tries to be a mountain. This approach has three benefits:

1. **Lower latency** (no rendering stack between your legs and the resistance)
2. **Predictable training load** (you can replicate the same climb exactly)
3. **Hardware independence** (use whatever actuator you already own)

It's the difference between a movie about a climb and climbing the road itself.

---

## 🧪 Testing & Validation

The library is validated using a hardware-in-the-loop rig with a 12V stepper and a resistance strain gauge. The validation procedure:
- Profile: Alpe d'Huez variant (21 switchbacks, 7.4 km, avg 7.9%)
- Expected resistance range: 0.8 N·m to 4.2 N·m
- Measured error: ±3% at steady-state, ±6% during rapid transitions

All test scripts are in `test/` and are configured for CI usage.

---

## 🤝 Contributing

We welcome contributions in any language (code, docs, or translations). Please open an issue before submitting a large PR. All code must pass the built-in formatting check (`clang-format`) and include comments in at least two supported languages.

---

## 📝 License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for full legal details.

---

## ⚠️ Disclaimer

**Use at your own risk.** Simcline-V2 interacts with physical resistance actuators. Incorrect wiring, overvoltage, or mechanical failure may cause injury or damage. Always:
- Disconnect power before adjusting wiring.
- Verify your actuator's current rating against your power source.
- Keep limbs clear of moving parts.
- Consult a physician before starting any new exercise program.

The authors are not responsible for hardware damage or personal injury arising from the use of this library. Fitspiration, not compensation.

---

## 🗓️ Version History (2026)

- **2.4.0** (Jan 2026) – Added multilingual support, event callbacks, and GPX conversion tool.
- **2.3.1** – Fixed timing drift on ESP32 with Bluetooth active.
- **2.2.0** – Introduced the non-blocking scheduler; removed old delay-based design.
- **2.0.0** – Complete rewrite; modular actuator interface.

---

## 🧠 SEO-Friendly Keywords

- Arduino bike trainer simulation
- indoor cycling incline motor control
- smart resistance library
- slope emulation Arduino
- stepper motor cycling
- magnetic brake PWM control
- open source fitness hardware

---

## ❓ Frequently Asked Questions

**Q: Can I use this with a smart trainer like Wahoo or Tacx?**
A: Not directly—it's designed for DIY custom actuators. However, you can use an ESP32 to bridge their BLE protocol and feed slope data into this library.

**Q: Does it work with electric bike motors?**
A: No. This library is for resistance devices, not drive motors.

**Q: My stepper is very noisy. What's the fix?**
A: Reduce the actuator gain and increase interpolation smoothing using the `setSmoothingFactor()` method (default = 0.8).

**Q: Can I run two actuators at once?**
A: Yes, instantiate two SimclineV2 objects with different pins. The internal scheduler handles both.

---

## 💬 Community & Support

We maintain a 24/7 discussion board (community-run) where you can share profiles, ask hardware questions, and show off your builds. The core developer typically responds within 48 hours.

For priority support, check the repository's issue tracker—we aim to answer every query before the next minor release.

---

## 🙏 Acknowledgments

This project was inspired by the thousands of hours of dull indoor training performed by its author. We extend thanks to the open-source motor control community and the tinkering cyclists who share their resistance profiles freely.

---

*Simcline-V2 – turn your static rig into a moving ridge, and your monotonous training into mountain stories.*