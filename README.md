# Formula 1 Rules & Motorsport IoT Sensors Explorer (Upgraded)

[![Deploy with Vercel](https://vercel.com/button)](https://vercel.com/new/clone?repository-url=https://github.com/chandan-bk/formula1-sensors-upgraded)

An interactive, explodable 3D Formula 1 vehicle model and telemetry simulator built with **Three.js** and **Vite**, demonstrating aerodynamic principles, vehicle sub-assemblies, and **32 simulated F1 sensor channels** with an integrated fault-injection and diagnostic simulation engine.

> **Educational Simulation Notice**  
> This project is an educational simulation and visualization tool. It contains **32 logical simulated sensor channels** and **55 visual 3D mounting markers** placed on a representative 3D Formula 1 vehicle model. All telemetry streams, oscilloscope traces, sampling rates, and diagnostic fault scenarios are generated in real time by the application's client-side simulation engine. The application **does not acquire real-world Formula 1 vehicle telemetry, connect to physical F1 hardware, or implement proprietary team or FIA electronic control units**.

👉 **Live Demo**: [https://chandan-bk.github.io/formula1-sensors-upgraded/](https://chandan-bk.github.io/formula1-sensors-upgraded/)

---

## 1. Quick Start

### Prerequisites
- **Node.js**: `^20.19.0 || >=22.12.0` (required by Vite 8). *Note: Node.js 18 is not supported.*
- **Web Browser**: Any modern web browser with WebGL 2.0 hardware acceleration enabled (Chrome, Firefox, Edge, Safari).

### Installation & Startup

```bash
# Clone the repository
git clone https://github.com/chandan-bk/formula1-sensors-upgraded.git
cd formula1-sensors-upgraded

# Install dependencies
npm install

# Start local development server (serves on http://localhost:3000)
npm run dev

# Build production bundle
npm run build

# Preview production build locally
npm run preview
```

---

## 2. Project Overview & Architecture

The application is structured into decoupled, modular systems:

```
src/
├── car/                     # Procedural 3D Car Model & Sensors Hardware
│   ├── aero.js              # Front & rear active-aero wings, flaps, DRS linkages
│   ├── body.js              # Chassis floor, sidepods, engine cover, nosecone
│   ├── build.js             # Master assembly coordinator & registry linking
│   ├── cockpit.js           # Halo, driver model, steering wheel, pedal box
│   ├── corners.js           # Brake assemblies, uprights, suspension wishbones, wheels
│   ├── dims.js              # Regulatory reference dimensions
│   ├── geometry.js          # Lofted aerodynamic surfaces & compound curves
│   ├── internals.js         # ICE block, MGU-K, battery energy store, radiators
│   ├── materials.js         # Physically-based rendering (PBR) carbon, titanium, rubber
│   ├── registry.js          # Part lookup, explode vector transforms, bounding boxes
│   └── sensors.js           # 3D procedural sensor markers, halos & signal links
├── config/
│   └── sensorsConfig.js     # Feature flags (ENABLE_SENSORS, DEBUG_SENSORS)
├── data/
│   ├── parts.js             # Regulatory component classifications (LTC, OSC, SSC, PDC, TRC)
│   ├── sensorDetails.js     # Representative operating principles, spec grids & checklists
│   ├── sensors.js           # Master catalogue of 32 simulated telemetry channels
│   └── telemetry.js         # Coupled 30 Hz lap simulator & fault injection engine
├── dimensions.js            # Interactive regulatory measurement callouts
├── studio.js                # Three.js floor shadows, contact occlusion, and studio lighting
├── main.js                  # Main render loop, post-processing composer, and event coordinator
├── style.css                # Motorsport-inspired dark theme UI styling
└── ui/
    ├── callout.js           # Dynamic floating pinned card, SVG leader line, 3 technical tabs
    ├── liveScope.js         # Real-time HTML5 Canvas oscilloscope with threshold limit lines
    ├── panels.js            # Top bar presets, explode slider, layer checkboxes, X-ray mode
    ├── sensorPreview.js     # 360-degree turntable inspector modal with safe resource lifecycle
    └── sensorsUI.js         # Floating sensor network HUD, mini track map, search & tour
```

### Rendering & Performance Optimization
- **Demand Rendering (`requestRender`)**: Renders frames only when camera orbits, animations tween, or UI state changes, eliminating idle GPU consumption.
- **PBR Materials & GTAOPass**: Ground-Truth Ambient Occlusion grounds the vehicle on the floor, while PCF soft shadow maps update during geometry transformations.
- **Memory Management & Safe Lifecycle**: Geometry and texture singletons are cached and reused across marker instances. The 360° turntable sensor inspector cleanly disposes per-model geometries and non-shared materials without leaking WebGL resources or disposing shared materials.
- **Hardware Fallback**: Integrated WebGL 2.0 detection verifies capabilities prior to scene initialization, displaying an informative fallback interface if WebGL 2.0 is unavailable.

---

## 3. Sensor Catalogue & 3D Hardware Markers

### Understanding 32 Logical Channels vs. 55 Physical 3D Markers
A fundamental architectural distinction exists between logical telemetry channels and visual 3D marker instances:

1. **32 Logical Catalogue Channels**: Represents the master telemetry database organized into 8 vehicle engineering subsystems. Each channel defines a simulated physical operating principle, representative calibration range, warning/critical safety thresholds, conceptual signal path, and diagnostic troubleshooting checklist.
2. **55 Visual 3D Hardware Marker Instances**: Represents physical sensor locations mounted across the vehicle chassis in the 3D scene.
3. **Why These Numbers Differ**: Formula 1 cars feature 4 symmetric wheel/brake/suspension corners (wheel speed, tyre pressure TPMS, tyre surface infrared, carbon disc pyrometer, pushrod strain load cell, linear damper potentiometer) and bilateral mirrored components (dual radiator banks for oil/coolant, dual exhaust pyrometers, active aero flap actuators across left and right wing elements). While each corner and bank has its own visual 3D marker mounted on the car model (totaling 55 instances), telemetry engineering groups these into 32 logical telemetry channels in the master catalogue.
4. **Simulated Telemetry vs Physical Hardware**: All live readings, oscilloscope waveforms, simulated sample rates, and diagnostic faults shown in this application are generated by a client-side JavaScript 30 Hz vehicle dynamics and telemetry simulation engine (`src/data/telemetry.js`). These values represent realistic engineering models for educational and demonstrative purposes, rather than direct live radio telemetry ingestion from an active Formula 1 racing car.

### Master Catalogue Summary (32 Simulated Telemetry Channels)

| Subsystem | Sensor Name | Channel Key | Simulated Class | Simulated Rate | Nominal Range |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Wheels & Tyres** | Wheel Speed Sensor | `wheelSpeed` | Control | 1,000 Hz | 0 – 290 rad/s |
| | Tyre Pressure (TPMS) | `tpms` | Monitoring | 10 Hz | 1.38 – 1.95 bar |
| | Tyre Surface IR Array | `tyreSurf` | Instrumentation | 100 Hz | 75 – 122 °C |
| **Brakes & Suspension** | Carbon Brake Disc Pyrometer | `discTemp` | Monitoring | 100 Hz | 350 – 850 °C |
| | Brake-by-Wire Hydraulic Pressure | `brakePress` | Control | 1,000 Hz | 0 – 135 bar |
| | Pushrod Strain Load Cell | `pushrod` | Instrumentation | 500 Hz | 2.0 – 12.5 kN |
| | Linear Damper Travel Potentiometer | `damper` | Instrumentation | 1,000 Hz | 10 – 52 mm |
| | Dynamic Laser Ride Height | `rideHeight` | Instrumentation | 500 Hz | 7.5 – 38 mm |
| | Steering Column Angle & Torque | `steer` | Control | 1,000 Hz | –140° to +140° |
| **Aero & Airflow** | Nose Dynamic Pitot-Static Array | `airspeed` | Instrumentation | 200 Hz | 0 – 360 km/h |
| | Active-Aero Flap Position LVDT | `aeroFlap` | Control | 1,000 Hz | 2° – 30° |
| | Rear Wing Pylon Strain Array | `wingStrain` | Instrumentation | 500 Hz | 400 – 2,400 µε |
| | Ambient Air Temp & Baro Probe | `ambient` | Monitoring | 10 Hz | 18 – 42 °C |
| **Chassis & Safety** | Chassis 6-Axis IMU | `imu` | Control | 1,000 Hz | –4.8 to +4.8 g |
| | Accident Data Recorder (ADR) | `adrG` | Monitoring | 1,000 Hz | 0 – 8.0 g (impact) |
| | Timing & Sector Transponder | `lapDist` | Monitoring | 20 Hz | 0 – 5,120 m |
| **Driver & Cockpit** | Pedal Assembly Dual Potentiometer | `throttle` | Control | 1,000 Hz | 0 – 100 % |
| | Driver Biometric Glove (PPG) | `heartRate` | Monitoring | 4 Hz | 85 – 188 bpm |
| | In-Ear 3-Axis Crash Accelerometer | `headG` | Monitoring | 1,000 Hz | 0 – 5.5 g |
| | Marshalling Light Display | `flag` | Monitoring | 10 Hz | Green / Yellow / Red / SC |
| **Engine & Fuel** | Crankshaft & Camshaft RPM Hall | `rpm` | Control | 20,000 Hz | 4,200 – 14,750 rpm |
| | Fuel Mass Flow Meter | `fuelFlow` | Monitoring | 100 Hz | 0 – 3,000 MJ/h |
| | Common-Rail Injection Pressure | `fuelPress` | Control | 2,000 Hz | 200 – 505 bar |
| | Oil & Coolant Temperature Probes | `coolant` | Monitoring | 20 Hz | 78 – 116 °C |
| | Exhaust Pyrometer & Wideband Lambda | `lambda` | Control | 100 Hz | 0.88 – 1.18 λ |
| | Turbocharger Shaft Speed Sensor | `turbo` | Monitoring | 10,000 Hz | 40 – 124 krpm |
| **Hybrid & Drivetrain** | MGU-K Resolver & Current Clamps | `mgukPower` | Control | 10,000 Hz | –350 to +350 kW |
| | Battery BMS Cell Voltage Shunt | `soc` | Monitoring | 100 Hz | 8 – 96 % SoC |
| | Driveshaft Telemetry Torque Collar | `shaftTorque` | Instrumentation | 1,000 Hz | –600 to +4,250 Nm |
| | Gearbox Selector Barrel Sensor | `gear` | Control | 1,000 Hz | 1 – 8 gears |
| **Electronics & Comms** | Central Electronic Control Unit | `canLoad` | Control | 1,000 Hz | 35 – 82 % load |
| | Telemetry Antenna Link | `rssi` | Monitoring | 100 Hz | –84 to –40 dBm |

---

## 4. Telemetry Physics & Fault Simulation Engine

### Consistent Vehicle Physics Simulation
Telemetry is not randomly generated. All 32 channels evolve from an integrated 84-second simulated Grand Prix lap (`src/data/telemetry.js`):
- **Braking**: Throttle drops to 0%, brake pressure surges to 120+ bar, deceleration reaches –5.0g, kinetic energy regenerates into the battery via MGU-K, and carbon disc temperatures climb past 750 °C with progressive cooling on subsequent straights.
- **Cornering**: Lateral g spikes up to 4.5+ g, suspension pushrod loads transfer across axles, and steering angle correlates with chassis yaw rate.
- **Active Aero**: Flaps shift between low-drag **Straight Mode** (>205 km/h on straights) and high-downforce **Corner Mode** under cornering and braking.

### Operational Sensor Fault Injection
The application features an interactive fault-injection suite allowing users to explore representative sensor failure modes:

1. **Disconnected (Open Circuit)**: Simulates pin pull-out or severed wiring loom. Signal drops immediately to baseline minimum (`OPEN_CKT`), 3D marker dims to slate gray, and scope trace flatlines.
2. **Stuck (Frozen Reading)**: Simulates frozen converter silicon or seized mechanical linkage. Value freezes at the instantaneous reading regardless of vehicle acceleration (`ADC_FROZEN`).
3. **High-Frequency Electrical Noise**: Simulates electrical noise interference coupling into unshielded analog signal lines, inducing high-frequency voltage fluctuations (`EMI_NOISE`).
4. **Out of Range (Saturation)**: Simulates transducer bridge short circuit or rail saturation exceeding normal full-scale operating limits (`SATURATION`).
5. **Signal Latency (Buffer Delay)**: Simulates buffer queue delay, introducing 3.0 seconds (90 frames at 30 Hz) of latency using genuine historical raw buffer values (`CAN_LAG`).
6. **Intermittent (Contact Chatter)**: Simulates road-vibration-induced terminal chatter, producing periodic signal dropouts (`INTERMITTENT` / `DROP_OUT`).

Each fault displays an informative diagnostic alert banner explaining:
- Diagnostic Failure Code (`OPEN_CKT`, `ADC_FROZEN`, `EMI_NOISE`, `SATURATION`, `CAN_LAG`, `INTERMITTENT`)
- Hypothetical root-cause explanation
- **Diagnostic Action Checklist** guiding users through representative physical troubleshooting
- One-click **"Restore Channel to Nominal"** recovery button that clears faults and restores nominal real-time readings.

---

## 5. End-to-End 6-Stage Telemetry Architecture & Technical Delineation

The data path architecture models the complete signal flow across 6 stages with clear delineation between real-world motorsport concepts, representative educational models, and in-browser simulation:

```
[ Stage 1: Sensing Element ] ➔ Physical transducer, actuator feedback, or virtual state estimation
            ▼
[ Stage 2: Signal Conditioning ] ➔ Differential amplification, anti-aliasing & digitisation
            ▼
[ Stage 3: Vehicle Network ] ➔ Deterministic vehicle bus architecture concept
            ▼
[ Stage 4: Central Processing ] ➔ Central Electronic Control Unit (ECU) concept
            ▼
[ Stage 5: Wireless Transmission ] ➔ Trackside wireless telemetry streaming concept
            ▼
[ Stage 6: Engineering Console ] ➔ Pit wall live oscilloscope waveforms, HUD & strategy console
```

### Architectural Delineation:
- **Motorsport Engineering Concepts**: Inspired by published automotive and motorsport engineering literature and regulatory principles (centralized ECUs, deterministic vehicle buses, non-contact pyrometers, accident data recorders).
- **Representative Educational Model**: The 6-stage telemetry model structure presented in the UI demonstrates instrumentation engineering concepts. Specifications regarding bus protocols, wireless streaming, and ECU processing represent conceptual educational models.
- **In-Browser JavaScript Simulation**: How this application models vehicle dynamics, sensor signal processing, and fault injection in real time at 30 Hz in the browser DOM and Three.js WebGL pipeline.

---

## 6. Engineering & Regulatory Context

1. **Anti-Lock Braking (ABS) & Traction Control (TC) Clarification**:
   Under Formula 1 technical regulations, conventional ABS and driver-assist traction control systems are prohibited. Wheel speed sensors are utilized exclusively for permitted engineering calculations:
   - Wheel slip ratio estimation
   - Rear Brake-by-Wire friction versus MGU-K regen torque migration
   - Differential ramp preload scheduling
   - Launch clutch engagement control
   - Regulatory auditing to verify the absence of prohibited traction control algorithms.
2. **Central Electronic Control Unit (ECU)**:
   In this educational application, central vehicle ECU behavior is represented via a simulation model that coordinates vehicle dynamics and telemetry channels.
3. **Illustrative vs Real-World Data**:
   All telemetry traces, sample rates, and calibration thresholds represent realistic engineering approximations for educational and demonstrative purposes. Official team calibrations and telemetry setups remain proprietary.

---

## 7. Deployment to Vercel & GitHub Pages

```bash
# Production deployment via Vercel CLI
npx vercel --prod
```
The application builds cleanly to `./dist` via Vite and can be hosted on GitHub Pages or any static web host:
[https://chandan-bk.github.io/formula1-sensors-upgraded/](https://chandan-bk.github.io/formula1-sensors-upgraded/)

---

## 8. References & Technical Standards

- **Formula 1 Technical Regulations (2026 Issue)** — Reference concepts on Electrical Systems, Powertrain Energy Limits, and Active Aerodynamics.
- **Motorsport Systems Literature** — Published motorsport engineering overviews of central ECU architectures, vehicle buses, and telemetry logging.
- **Sensor Technology References** — Miniature high-temperature pyrometers, strain gauge load cells, and differential Hall speed transducers.
