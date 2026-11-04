# Formula 1 Rules & Motorsport IoT Sensors Explorer (Upgraded)

[![Deploy with Vercel](https://vercel.com/button)](https://vercel.com/new/clone?repository-url=https://github.com/chandan-bk/formula1-sensors-upgraded)

An interactive, explodable 3D Formula 1 vehicle model and telemetry simulator built with **Three.js** and **Vite**, demonstrating the **2026 FIA Formula 1 Technical Regulations** and **32 simulated F1 sensor and telemetry channels** with an integrated fault-injection and diagnostic simulation engine.

This application is an educational simulation using representative engineering models. All telemetry streams, vehicle dynamics calculations, and sensor fault scenarios are computed in real time in client-side JavaScript.

👉 **Live Demo**: [https://chandan-bk.github.io/formula1-sensors-upgraded/](https://chandan-bk.github.io/formula1-sensors-upgraded/)

---

## 1. Quick Start

### Prerequisites
- Node.js (Vite 8 requirement: `^20.19.0 || >=22.12.0`)
- Modern web browser with WebGL 2.0 hardware acceleration enabled

### Installation & Startup

```bash
# Clone the repository
git clone https://github.com/chandan-bk/formula1-sensors-upgraded.git
cd formula1-sensors-upgraded

# Install dependencies
npm install

# Start development server
npm run dev

# Build production bundle
npm run build

# Preview production build locally
npm run preview
```

---

## 2. Architecture & Rendering Pipeline

The application is structured into decoupled, modular systems:

```
src/
├── car/                     # Procedural 3D Car Model & Sensors Hardware
│   ├── aero.js              # Front & rear active-aero wings, flaps, DRS linkages
│   ├── body.js              # Chassis floor, sidepods, engine cover, nosecone
│   ├── build.js             # Master assembly coordinator & registry linking
│   ├── cockpit.js           # Halo, driver model, steering wheel, pedal box
│   ├── corners.js           # Brake assemblies, uprights, suspension wishbones, wheels
│   ├── dims.js              # 2026 FIA regulatory reference dimensions
│   ├── geometry.js          # Lofted aerodynamic surfaces & compound curves
│   ├── internals.js         # ICE block, MGU-K, battery energy store, radiators
│   ├── materials.js         # Physically-based rendering (PBR) carbon, titanium, rubber
│   ├── registry.js          # Part lookup, explode vector transforms, bounding boxes
│   └── sensors.js           # 3D procedural IoT sensor hardware, halos & signal links
├── config/
│   └── sensorsConfig.js     # Feature flags (ENABLE_SENSORS, DEBUG_SENSORS)
├── data/
│   ├── parts.js             # FIA 2026 regulatory component classifications (LTC, OSC, SSC, PDC, TRC)
│   ├── sensorDetails.js     # Physics principles, specs grids, and investigation checklists
│   ├── sensors.js           # Master catalogue of 32 motorsport telemetry channels
│   └── telemetry.js         # Physically coupled 30 Hz lap simulator & fault engine
├── dimensions.js            # Interactive FIA regulatory measurement callouts
├── studio.js                # Three.js floor shadows, contact occlusion, and studio lighting
├── main.js                  # Main render loop, post-processing composer, and event coordinator
├── style.css                # Motorsport-inspired dark theme UI styling
└── ui/
    ├── callout.js           # Dynamic floating pinned card, SVG leader line, 3 technical tabs
    ├── liveScope.js         # Real-time HTML5 Canvas oscilloscope with threshold limit lines
    ├── panels.js            # Top bar presets, explode slider, layer checkboxes, X-ray mode
    ├── sensorPreview.js     # 360-degree turntable inspector modal
    └── sensorsUI.js         # Floating sensor network HUD, mini track map, search & tour
```

### Rendering & Performance Optimization
- **Demand Rendering (`requestRender`)**: Draws frames only when camera orbits, animations tween, or UI state changes, eliminating idle GPU consumption.
- **PBR Materials & GTAOPass**: Ground-Truth Ambient Occlusion grounds the car on the floor, while PCF soft shadow maps update only during geometry transforms.
- **Memory Management**: Geometry and texture singletons are cached and reused across marker instancing. Dynamic previews in the 360° inspector feature clean unmount routines to prevent WebGL context leaks.
- **Hardware Fallback**: Integrated WebGL 2.0 detection with an informative, non-blocking fallback interface for unsupported hardware or headless environments.

---

## 3. Sensor Catalogue & 3D Hardware Markers

### Understanding 32 Logical Channels vs. 55 Physical 3D Markers
A fundamental distinction exists between logical telemetry channels and physical 3D marker instances:

1. **32 Logical Catalogue Channels**: Represents the master telemetry database organized into 8 vehicle engineering subsystems. Each channel defines a physical operating principle, calibration range, warning/critical safety thresholds, signal conditioning path, and diagnostic troubleshooting checklist.
2. **55 Visual 3D Hardware Marker Instances**: Represents physical sensor hardware installed across the vehicle chassis.
3. **Why These Numbers Differ**: Formula 1 cars feature 4 symmetric wheel/brake/suspension corners (wheel speed, tyre pressure TPMS, tyre surface infrared, carbon disc pyrometer, pushrod strain load cell, linear damper potentiometer) and bilateral mirrored components (dual radiator banks for oil/coolant, dual turbo exhaust pyrometers, active aero flap actuators across left and right wing elements). While each corner and bank has its own physical sensor hardware installed on the vehicle geometry (totaling 55 3D marker instances), telemetry engineering groups these into 32 logical telemetry channels in the master catalogue.
4. **Simulated Telemetry vs Physical Hardware Measurements**: All live readings, oscilloscope waveforms, sampling rates, and diagnostic faults shown in this application are generated by a client-side JavaScript 30 Hz vehicle dynamics and telemetry simulation engine (`src/data/telemetry.js`). These values represent realistic engineering models for educational and demonstrative purposes, rather than direct live radio telemetry ingestion from an active Formula 1 racing car.

### Master Catalogue Summary (32 Telemetry Channels)

| Subsystem | Sensor Name | Channel Key | Authority | Category | Sample Rate | Normal Range |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **Wheels & Tyres** | Wheel Speed Sensor | `wheelSpeed` | Team | Control | 1,000 Hz | 0 – 290 rad/s |
| | Tyre Pressure (TPMS) | `tpms` | FIA | Monitoring | 10 Hz | 1.38 – 1.95 bar |
| | Tyre Surface IR Array | `tyreSurf` | Team | Instrumentation | 100 Hz | 75 – 122 °C |
| **Brakes & Suspension** | Carbon Brake Disc Pyrometer | `discTemp` | Team | Monitoring | 100 Hz | 350 – 850 °C |
| | Brake-by-Wire Hydraulic Pressure | `brakePress` | Team | Control | 1,000 Hz | 0 – 135 bar |
| | Pushrod Strain Load Cell | `pushrod` | Team | Instrumentation | 500 Hz | 2.0 – 12.5 kN |
| | Linear Damper Travel Potentiometer | `damper` | Team | Instrumentation | 1,000 Hz | 10 – 52 mm |
| | Dynamic Laser Ride Height | `rideHeight` | Team | Instrumentation | 500 Hz | 7.5 – 38 mm |
| | Steering Column Angle & Torque | `steer` | Team | Control | 1,000 Hz | –140° to +140° |
| **Aero & Airflow** | Nose Dynamic Pitot-Static Array | `airspeed` | Team | Instrumentation | 200 Hz | 0 – 360 km/h |
| | Active-Aero Flap Position LVDT | `aeroFlap` | FIA | Control | 1,000 Hz | 2° – 30° |
| | Rear Wing Pylon Strain Array | `wingStrain` | Team | Instrumentation | 500 Hz | 400 – 2,400 µε |
| | Ambient Air Temp & Baro Probe | `ambient` | FIA | Monitoring | 10 Hz | 18 – 42 °C |
| **Chassis & Safety** | Chassis 6-Axis IMU | `imu` | Team | Control | 1,000 Hz | –4.8 to +4.8 g |
| | Accident Data Recorder (ADR) | `adrG` | FIA | Monitoring | 1,000 Hz | 0 – 8.0 g (impact) |
| | Timing & Sector Transponder | `lapDist` | FIA | Monitoring | 20 Hz | 0 – 5,120 m |
| **Driver & Cockpit** | Pedal Assembly Dual Potentiometer | `throttle` | Team | Control | 1,000 Hz | 0 – 100 % |
| | Driver Biometric Glove (PPG) | `heartRate` | FIA | Monitoring | 4 Hz | 85 – 188 bpm |
| | In-Ear 3-Axis Crash Accelerometer | `headG` | FIA | Monitoring | 1,000 Hz | 0 – 5.5 g |
| | Marshalling Light Display | `flag` | FIA | Monitoring | 10 Hz | Green / Yellow / Red / SC |
| **Engine & Fuel** | Crankshaft & Camshaft RPM Hall | `rpm` | Team | Control | 20,000 Hz | 4,200 – 14,750 rpm |
| | Fuel Mass Flow Meter | `fuelFlow` | FIA | Monitoring | 100 Hz | 0 – 3,000 MJ/h |
| | Common-Rail Injection Pressure | `fuelPress` | Team | Control | 2,000 Hz | 200 – 505 bar |
| | Oil & Coolant Temperature Probes | `coolant` | Team | Monitoring | 20 Hz | 78 – 116 °C |
| | Exhaust Pyrometer & Wideband Lambda | `lambda` | Team | Control | 100 Hz | 0.88 – 1.18 λ |
| | Turbocharger Shaft Speed Sensor | `turbo` | Team | Monitoring | 10,000 Hz | 40 – 124 krpm |
| **Hybrid & Drivetrain** | MGU-K Resolver & Current Clamps | `mgukPower` | Team | Control | 10,000 Hz | –350 to +350 kW |
| | Battery BMS Cell Voltage Shunt | `soc` | FIA | Monitoring | 100 Hz | 8 – 96 % SoC |
| | Driveshaft Telemetry Torque Collar | `shaftTorque` | FIA | Instrumentation | 1,000 Hz | –600 to +4,250 Nm |
| | Gearbox Selector Barrel Sensor | `gear` | Team | Control | 1,000 Hz | 1 – 8 gears |
| **Electronics & Comms** | Central Electronic Control Unit | `canLoad` | FIA | Control | 1,000 Hz | 35 – 82 % load |
| | Telemetry Antenna Link | `rssi` | Team | Monitoring | 100 Hz | –84 to –40 dBm |

---

## 4. Telemetry Physics & Fault Simulation Engine

### Consistent Vehicle Physics Simulation
Telemetry is not randomly generated. All 32 channels evolve from an integrated 84-second simulated Grand Prix lap (`src/data/telemetry.js`):
- **Braking**: Throttle drops to 0%, brake pressure surges to 120+ bar, deceleration reaches –5.0g, kinetic energy regenerates into the battery via MGU-K, and carbon disc temperatures climb past 750 °C with progressive cooling on subsequent straights.
- **Cornering**: Lateral g spikes up to 4.5+ g, suspension pushrod loads transfer across axles, and steering angle correlates with chassis yaw rate.
- **Active Aero**: Flaps shift between low-drag **Straight Mode** (>205 km/h on straights) and high-downforce **Corner Mode** under cornering and braking.

### Operational Sensor Fault Injection
The application features an interactive fault-injection suite allowing engineers and students to simulate representative sensor failure modes:

1. **Disconnected (Open Circuit)**: Simulates pin pull-out or severed wiring loom. Signal drops immediately to baseline minimum (`OPEN_CKT`), 3D marker dims to slate gray, and scope trace flatlines.
2. **Stuck (Frozen Reading)**: Simulates frozen converter silicon or seized mechanical linkage. Value freezes at the instantaneous reading regardless of vehicle acceleration (`ADC_FROZEN`).
3. **High-Frequency Electrical Noise**: Simulates electrical noise interference coupling into unshielded analog signal lines, inducing high-frequency voltage fluctuations (`EMI_NOISE`).
4. **Out of Range (Saturation)**: Simulates transducer bridge short circuit or rail saturation exceeding normal full-scale operating limits (`SATURATION`).
5. **Signal Latency (Buffer Delay)**: Simulates buffer queue delay, introducing 3.0 seconds (90 frames at 30 Hz) of latency using true historical raw buffer values (`CAN_LAG`).
6. **Intermittent (Contact Chatter)**: Simulates road-vibration-induced terminal chatter, producing periodic signal dropouts (`INTERMITTENT` / `DROP_OUT`).

Each fault displays an informative diagnostic alert banner explaining:
- Diagnostic Failure Code (`OPEN_CKT`, `ADC_FROZEN`, `EMI_NOISE`, `SATURATION`, `CAN_LAG`, `INTERMITTENT`)
- Hypothetical root-cause explanation
- **Diagnostic Action Checklist** guiding users through representative physical troubleshooting
- One-click **"Restore Channel to Nominal"** recovery button.

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
[ Stage 5: Wireless Transmission ] ➔ Trackside wireless telemetry RF streaming concept
            ▼
[ Stage 6: Engineering Console ] ➔ Pit wall live oscilloscope waveforms, HUD & strategy console
```

### Architectural Delineation:
- **Motorsport Engineering Concepts**: Inspired by published motorsport engineering literature and regulatory principles (centralized ECUs, deterministic vehicle buses, non-contact pyrometers, accident data recorders).
- **Representative Educational Model**: The 6-stage telemetry model structure presented in the UI demonstrates instrumentation engineering concepts. Specifications regarding bus protocols, RF modulation, and ECU processing represent conceptual educational models.
- **In-Browser JavaScript Simulation**: How this application models vehicle dynamics, sensor signal processing, and fault injection in real time at 30 Hz in the browser DOM and Three.js WebGL pipeline.

---

## 6. Engineering & Regulatory Context

1. **Anti-Lock Braking (ABS) & Traction Control (TC) Clarification**:
   Under FIA Formula 1 Technical Regulations (Articles 10 and 11), conventional ABS and driver-assist traction control systems are prohibited. Wheel speed sensors are utilized exclusively for legal engineering calculations:
   - Wheel slip ratio estimation
   - Rear Brake-by-Wire friction versus MGU-K regen torque migration
   - Differential ramp preload scheduling
   - Launch clutch engagement control
   - Compliance auditing to verify the absence of prohibited traction control algorithms.
2. **Central Electronic Control Unit (ECU)**:
   In this educational application, central vehicle ECU behavior is represented via a representative simulation model that coordinates vehicle dynamics and telemetry channels.
3. **Illustrative vs Real-World Data**:
   All telemetry traces, sample rates, and calibration thresholds represent realistic engineering approximations for educational and demonstrative purposes. Official team calibrations and telemetry setups remain proprietary.

---

## 7. Deployment to Vercel & GitHub Pages

```bash
# Production deployment via Vercel CLI
npx vercel --prod
```
The application builds cleanly to `./dist` via Vite and is deployed live on GitHub Pages:
[https://chandan-bk.github.io/formula1-sensors-upgraded/](https://chandan-bk.github.io/formula1-sensors-upgraded/)

---

## 8. References & Technical Standards

- **FIA Formula 1 Technical Regulations (2026 Issue)** — Articles on Electrical Systems, Powertrain Energy Limits, and Active Aerodynamics.
- **Motorsport Systems Literature** — Published motorsport engineering overviews of central ECU architectures, CAN buses, and telemetry logging.
- **Sensor Technology References** — Miniature high-temperature pyrometers, strain gauge load cells, and differential Hall speed transducers.
