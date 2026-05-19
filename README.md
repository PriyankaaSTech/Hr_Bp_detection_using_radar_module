# HLK-LD6002 Radar Vital-Sign Monitor
**Non-contact Heart Rate & Respiratory Rate Detection — ESP32 + 60 GHz mmWave Radar**

---

## What This Does

This project implements a robust, 8-stage DSP pipeline on an ESP32 that reads raw output from the HLK-LD6002 60 GHz mmWave radar and produces clean, stable heart rate and respiratory rate readings with zero physical contact with the subject.

---

## Hardware

| Component | Part |
|-----------|------|
| Microcontroller | ESP32 (any dev board) |
| Radar Sensor | HLK-LD6002 (60 GHz FMCW mmWave) |
| Power | 3.3V LDO >= 600 mA |

**Wiring:**

| LD6002 Pin | Signal | ESP32 Pin |
|------------|--------|-----------|
| Pin 1 (3V3) | Power | 3V3 |
| Pin 2 (GND) | Ground | GND |
| Pin 7 (TX0) | UART Out | GPIO 16 (RX2) |
| Pin 8 (RX0) | UART In | GPIO 17 (TX2) |

---

## Signal Processing Pipeline

Each channel passes through 8 stages:

1. IQR Pre-screen — drop statistical outliers
2. Physiological Range Gate — HR: 40–120 bpm, RR: 4–35 bpm
3. Adaptive Step Gate — reject impossible frame-to-frame jumps
4. Median Filter (N=5) — remove spike noise
5. Stddev Quality Gate — reject noisy windows
6. Double EMA (DEMA) — lag-cancelled smoothing
7. Confirmation Streak (N=3) — require N consistent readings
8. Variance-Locked Output — print only when stable and changed

---

## Quick Setup

1. Install the LD6002 library: https://github.com/phuongnamzz/HLK-LD6002
2. Open `kiro/kiro.ino` in Arduino IDE, select ESP32 Dev Module, and upload
3. Open Serial Monitor at 115200 baud and stand within 0.4–1.5 m of the sensor

---

## Detection Parameters

| Parameter | Value |
|-----------|-------|
| Detection distance | 0.4 m – 1.5 m |
| Heart rate range | 60 – 150 bpm |
| Respiratory rate range | 9 – 48 bpm |
| Time to first output | ~16 seconds |
| Presence timeout | 6 seconds without valid distance |

---

## Project Structure

```
kiro/
├── kiro.ino
├── dsp_config.h
├── circular_buffer.h
├── signal_filters.h
├── signal_preprocessor.h
├── presence_detector.h
├── vital_estimator.h
├── debug_logger.h
└── docs/
    ├── 01_Functional_Document.docx
    ├── 02_Technical_Specification.docx
    └── 03_Architecture_Diagrams.docx
```

---

## Known Limitations

- Single target only — multiple people produce unreliable readings
- Subject must stay within 0.4–1.5 m
- ~60 second warm-up required after power-on
- Large body movements temporarily reset the confirmation streak

---

## License

MIT — free to use, modify, and distribute.
