# Technical Stack @ Covenant

Covenant is built across eight engineering disciplines. This page describes the actual tools, languages, and systems we use — not aspirational tech, not buzzwords.

If you're applying, this is what you'll be working with.

---

## Edge Software (Vergil)

The daemon that runs on every station. It orchestrates all on-device processes, syncs with the cloud, and bridges hardware to software.

**Language & concurrency**
- Python 3 — primary language for all edge software
- asyncio — concurrency model throughout (no blocking I/O, no threads)

**On-station infrastructure**
- Docker + Docker Compose — Frigate, Mosquitto, and all workers run as containers
- systemd — Vergil itself runs as a managed service
- Mosquitto (MQTT 2.0) — internal event bus between Frigate and workers
- Redis — VOD request queue between daemon and workers

**Video pipeline**
- Frigate NVR — object detection with TensorRT, clip recording
- MediaMTX — RTSP republishing and WebRTC endpoint (WHEP)
- ffmpeg / ffprobe — clip merging, stream inspection, thumbnail extraction

**Key libraries**
- `websockets` — WebSocket client to Flare
- `supabase-py` — cloud data sync and machine authentication
- `psutil`, `jtop` — system and Jetson-specific GPU/thermal metrics
- `aioredis` — async Redis client
- StrictYAML — configuration parsing

---

## Dashboard (Galleon)

The web interface where operators monitor stations, review detections, and manage the fleet.

**Languages & frameworks**
- TypeScript 5 (strict mode throughout)
- Next.js 16 with App Router
- React 19

**Styling**
- Tailwind CSS v4

**Data & auth**
- Supabase — PostgreSQL 15, Auth (Google OAuth + email), Realtime subscriptions, Storage
- Cloudflare R2 — object storage for clips and thumbnails (S3-compatible)
- Stripe — subscription billing and webhooks

**Video**
- WebRTC (WHEP) via MediaMTX — live camera feeds
- HLS.js — VOD playback with timeline scrubbing

**Real-time**
- Flare WebSocket — station presence and live status
- Supabase Realtime — detection event push updates

**Forms & validation**
- react-hook-form + zod

**Other**
- i18next — internationalization (Spanish / English)
- OpenAPI 3.0 — API documentation

---

## Real-time Layer (Flare)

A lightweight WebSocket server that tracks which stations are online and broadcasts presence updates to dashboard clients.

- Python + `websockets`
- asyncio — single-process, event-loop based
- Supabase JWT validation — all connections authenticated before admission
- In-memory session state — no external store, by design

---

## Station Control (Argus One local stack)

The on-station control stack that manages the physical docking station before events are forwarded to the cloud.

**Central control (Jetson)**
- Python 3.9+ with aiohttp — REST API and WebSocket server
- paho-mqtt — MQTT client connected to local Mosquitto broker
- pyserial — USB serial link to ATmega for telemetry
- JSON over MQTT — message format for all inter-device communication
- Web dashboard on port 8080 — HTML/CSS/JS, canvas-based battery gauge, real-time graphs

**Embedded firmware (PlatformIO + Arduino framework)**
- C++ on ESP32 (DOIT DevKit V1 / NodeMCU-32S)
- PubSubClient — MQTT client for ESP32 nodes
- ArduinoJson — JSON serialization/deserialization on device
- WiFi + MQTT — all ESP32 nodes connect to local Mosquitto on Jetson

**ESP32 nodes in the system**
- Energy monitor — 4× INA228 current/voltage sensors (I2C), solar/load/battery telemetry, relay switching with hysteresis
- Crane controller — winch servo (PWM, 50 Hz), electric lock, state machine (IDLE → LOWER → RAISE)
- PTZ controller — 2× 28BYJ-48 stepper motors with ULN2003 drivers, half-step sequencing

**Independent subsystems**
- Cooling system — ATmega328P + PID controller, temperature sensor, PWM fan output, HC-05 Bluetooth telemetry
- Gripper system — Raspberry Pi + RPi.GPIO, L298N motor driver, limit switches

**Battery system**
- LiFePO4 3S7P pack (22.2V nominal, 156.8 Wh)
- SOC estimation via OCV lookup table

---

## Hardware (Thunder Board)

A custom 6-layer PCB that powers, monitors, and reports on all station subsystems.

**Design tool**
- KiCad 9.0

**Microcontroller**
- ATmega328PB @ 16 MHz (AVR architecture)

**Peripherals & protocols**
- I2C — sensor bus (INA226, INA228, MCP9700T)
- UART / USB — serial link to Jetson via CH340C USB-UART adapter
- SPI — ISP programming

**Power electronics**
- XL4015 buck converters — 4 regulated output channels, up to 63V input
- INA226 / INA228 — current, voltage, and power monitoring per channel
- MCP9700T — temperature sensing per channel
- TVS diodes, polyfuses — transient and overcurrent protection

---

## Infrastructure

| Layer | Technology |
|---|---|
| Edge compute | NVIDIA Jetson Orin (Docker host, TensorRT inference) |
| Embedded MCUs | ESP32, ATmega328PB, ATmega328P, Raspberry Pi |
| Cloud database | Supabase (PostgreSQL 15 + RLS multi-tenancy) |
| Object storage | Cloudflare R2 (S3-compatible) |
| Remote access | ZeroTier (software VPN per station) |
| Containerization | Docker + Docker Compose |

---

## What we look for

We don't expect every candidate to know all of this. We do expect depth in at least one area and genuine curiosity about the rest.

| Discipline | Core skills |
|---|---|
| **AI / ML** | Python, computer vision, YOLO / TensorRT, WebGPU, ONNX |
| **Software Engineering** | TypeScript or Python, async patterns, video streaming, distributed systems |
| **Aerospace** | PX4, MAVSDK, ArUco / OpenCV, ROS 2, drone autonomy |
| **Electronics** | KiCad, analog power design, PCB layout, schematic design |
| **Embedded Systems** | C/C++, ESP32 / AVR / ARM, I2C / SPI / UART, PlatformIO, motor control |
| **Renewable Energy** | MPPT topology, LiFePO4 battery management, solar sizing, power electronics |
| **Cybersecurity** | Network security, embedded hardening, threat modeling |
| **Mechatronics** | CAD (SOLIDWORKS / Fusion 360), GD&T, design for fabrication |

---

## The challenge repo

The best way to show us what you can do is to complete one of the challenges in this repository.

[← Back to challenges](README.md)
