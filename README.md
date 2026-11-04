# Formula 1 Sensor Explorer — Educational 3D Telemetry Simulation

[![Deploy with Vercel](https://vercel.com/button)](https://vercel.com/new/clone?repository-url=https://github.com/chandan-bk/formula1-sensors-upgraded)

An interactive, explodable 3D vehicle model and telemetry simulator built with **Three.js** and **Vite**, demonstrating vehicle sub-assemblies, aerodynamic concepts, and **32 simulated sensor channels** with an integrated fault-injection and diagnostic simulation engine.

> **Educational Simulation Notice**  
> This project is an educational simulation and visualization tool. It contains **32 logical simulated sensor channels** and **55 visual 3D sensor marker instances** placed across a representative 3D racing car model. All telemetry streams, oscilloscope traces, sampling rates, vehicle dynamics calculations, and diagnostic fault scenarios are generated in real time by the application's client-side simulation engine. The application **does not acquire real-world Formula 1 vehicle telemetry, connect to physical F1 hardware, or reproduce proprietary team or sanctioning body electronic architectures**.

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
├── car/                     # Procedural 3D Car Model & Visual Sensor Markers
│   ├── aero.js              # Front & rear active-aero wings, flaps, DRS linkages
│   ├── body.js              # Chassis floor, sidepods, engine cover, nosecone
│   ├── build.js             # Master assembly coordinator & registry linking
│   ├── cockpit.js           # Cockpit protection halo, driver model, steering wheel, pedal box
│   ├── corners.js           # Brake assemblies, uprights, suspension wishbones, wheels
│   ├── dims.js              # Representative vehicle dimensions
│   ├── geometry.js          # Lofted aerodynamic surfaces & compound curves
│   ├── internals.js         # ICE block, MGU-K concept, battery energy store, radiators
│   ├── materials.js         # Physically-based rendering (PBR) carbon, titanium, rubber
│   ├── registry.js          # Part lookup, explode vector transforms, bounding boxes
│   └── sensors.js           # 3D procedural sensor markers, halos & signal links
├── config/
│   └── sensorsConfig.js     # Feature flags (ENABLE_SENSORS, DEBUG_SENSORS)
├── data/
│   ├── parts.js             # Engineering component categories (aerodynamics, powertrain, chassis)
│   ├── sensorDetails.js     # Representative operating principles, spec grids & checklists
│   ├── sensors.js           # Master catalogue of 32 simulated telemetry channels
│   └── telemetry.js         # Coupled 30 Hz lap simulator & fault injection engine
├── dimensions.js            # Interactive vehicle dimensional measurement callouts
├── studio.js                # Three.js floor shadows, contact occlusion, and studio lighting
├── main.js                  # Main render loop, post-processing composer, and event coordinator
├── style.css                # Motorsport-inspired dark theme UI styling
└── ui/
    ├── callout.js           # Dynamic floating pinned card, SVG leader line, technical tabs
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

### Understanding 32 Logical Channels vs. 55 Visual 3D Sensor Marker Instances
An important architectural distinction exists between the logical telemetry channels and visual 3D marker instances:

1. **32 Logical Catalogue Channels**: Represents the simulated telemetry database organized into 8 functional vehicle engineering subsystems. Each channel defines a simulated operating principle, illustrative calibration range, warning/critical safety thresholds, conceptual signal path, and diagnostic troubleshooting checklist.
2. **55 Visual 3D Sensor Marker Instances**: Represents interactive visual touchpoints mounted across the 3D car model. These markers indicate representative physical installation areas (e.g. left and right radiator banks, 4 wheel/brake/suspension corners, multi-element active wing actuators). They provide spatial context within the 3D scene and do not claim to mirror verified, physical Formula 1 sensor hardware configurations.
3. **Illustrative Simulation Parameters**: All sampling frequencies (e.g. 10 Hz to 20,000 Hz) and nominal operating ranges listed in the catalogue are illustrative simulation parameters configured for the educational model. They provide realistic engineering scales for real-time visualization rather than verified real-world hardware specifications.
4. **Simulated Telemetry vs Physical Measurements**: All live readings, waveforms, and fault scenarios are generated by a client-side JavaScript 30 Hz vehicle dynamics and telemetry simulation engine (`src/data/telemetry.js`).

### Master Catalogue Summary (32 Simulated Telemetry Channels)

| Subsystem | Sensor Name | Channel Key | Functional Role | Illustrative Sim Rate | Illustrative Sim Range |
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
| | Track Status Light Indicator | `flag` | Monitoring | 10 Hz | Green / Yellow / Red / SC |
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

### Simulated Physics Relationships vs Real-World Vehicle Dynamics
Telemetry values evolve from an integrated 84-second simulated circuit lap programmed in client-side JavaScript (`src/data/telemetry.js`):
- **Braking**: When throttle drops, brake pressure increases, negative longitudinal acceleration develops, simulated kinetic energy regenerates toward the energy store via the MGU-K model, and carbon disc temperatures rise with progressive cooling modeled on straights.
- **Cornering**: Lateral acceleration develops in turns, suspension pushrod load transfers across axles, and steering angle correlates with simulated chassis yaw rate.
- **Active Aero**: Flap angle models transition between lower-drag **Straight Mode** (>205 km/h on straights) and higher-downforce **Corner Mode** under cornering and braking.

*Note on Vehicle Dynamics*: While these physical relationships are coupled mathematically to produce realistic, intuitive telemetry traces, they are educational approximations designed for interactive exploration, not verified vehicle dynamics data or validated lap telemetry from a real Formula 1 car.

### Hypothetical Simulated Fault Scenarios
The application features an interactive fault-injection suite allowing users to observe hypothetical sensor failure modes:

1. **Disconnected (Zero/Baseline Drop)**: A hypothetical scenario simulating a signal loss or open-circuit condition, causing the channel reading to drop to its baseline minimum (`OPEN_CKT`) with a dimmed marker and flatline scope trace.
2. **Stuck (Frozen Value)**: A hypothetical scenario simulating a frozen reading where the channel holds its instantaneous reading fixed regardless of dynamic vehicle changes (`ADC_FROZEN`).
3. **High-Frequency Disturbance (Simulated Noise)**: A hypothetical scenario demonstrating high-frequency perturbations superimposed on the nominal telemetry waveform (`EMI_NOISE`).
4. **Out of Range (Scale Saturation)**: A hypothetical scenario simulating channel reading saturation beyond nominal operating envelope boundaries (`SATURATION`).
5. **Signal Latency (Buffer Delay)**: A hypothetical scenario modeling a 3.0-second (90 frames at 30 Hz) transmission or processing latency using true historical raw buffer values (`CAN_LAG`).
6. **Intermittent (Signal Dropout)**: A hypothetical scenario demonstrating periodic signal dropout and recovery intervals (`INTERMITTENT`).

Each fault displays an informative diagnostic alert banner explaining:
- Diagnostic Failure Code (`OPEN_CKT`, `ADC_FROZEN`, `EMI_NOISE`, `SATURATION`, `CAN_LAG`, `INTERMITTENT`)
- Hypothetical cause explanation
- **Diagnostic Action Checklist** offering representative troubleshooting steps
- One-click **"Restore Channel to Nominal"** recovery button that clears the simulated fault and restores nominal real-time readings.

---

## 5. End-to-End 6-Stage Telemetry Architecture & Technical Delineation

The conceptual data path models signal flow across 6 stages with clear delineation between engineering concepts, representative educational models, and in-browser simulation:

```
[ Stage 1: Sensing Element ] ➔ Transducer or actuator feedback concept
            ▼
[ Stage 2: Signal Conditioning ] ➔ Amplification, filtering & digitisation concept
            ▼
[ Stage 3: Vehicle Network ] ➔ Conceptual vehicle bus architecture
            ▼
[ Stage 4: Central Processing ] ➔ Central Electronic Control Unit (ECU) concept
            ▼
[ Stage 5: Wireless Transmission ] ➔ Trackside wireless telemetry streaming concept
            ▼
[ Stage 6: Engineering Console ] ➔ Pit wall live waveforms, HUD & diagnostic console
```

### Architectural Delineation:
- **Automotive Engineering Concepts**: Based on general automotive and motorsport engineering concepts (centralized electronic control units, multiplexed vehicle networks, non-contact pyrometers, and impact data recording).
- **Representative Educational Model**: The 6-stage telemetry model illustrates general instrumentation concepts. Subsystem descriptions (such as kinetic energy recovery, battery management, fuel flow monitoring, and wireless telemetry links) illustrate functional operating principles without claiming to reproduce proprietary team or manufacturer hardware.
- **In-Browser JavaScript Simulation**: How this application models vehicle dynamics, sensor signal processing, and fault injection in real time at 30 Hz in the browser DOM and Three.js WebGL pipeline.

---

## 6. Engineering Context & Modeling Principles

1. **Wheel-Speed Sensing in Vehicle Dynamics**:
   In high-performance automotive and motorsport applications, rotational wheel-speed measurement is fundamental to chassis dynamics analysis and powertrain control:
   - Estimating tyre slip ratio during acceleration and braking phases
   - Comparing corner-to-corner rotational speeds to evaluate yaw and handling characteristics
   - Assisting brake-by-wire balance calculations and energy regeneration coordination
   - Evaluating differential behavior and torque distribution across driven wheels
   - Cross-referencing vehicle speed estimates with accelerometer data.
2. **Simulated Control Unit Architecture**:
   In this educational application, supervisory vehicle control unit behavior is represented via a client-side simulation model that coordinates vehicle dynamics, lap progression, and sensor channel values.
3. **Illustrative Simulation Parameters**:
   All telemetry waveforms, sample rates, operating envelopes, and calibration thresholds represent illustrative simulation parameters chosen to demonstrate engineering principles. They do not represent proprietary manufacturer calibrations or verified racing team configurations.

---

## 7. Production Build & Deployment

The standard Vite build script compiles and bundles the project into static web assets within the `./dist` directory, suitable for deployment to any static web host (such as GitHub Pages or Vercel):

```bash
# Build the production bundle
npm run build

# Optional preview of the local build output
npm run preview
```

Online demonstration:
[https://chandan-bk.github.io/formula1-sensors-upgraded/](https://chandan-bk.github.io/formula1-sensors-upgraded/)

---

## 8. Further Reading and Engineering Context

This project draws upon general automotive engineering concepts and illustrative simulation parameters to provide an interactive educational tool:

- **General Engineering Principles**: The 3D vehicle assemblies, subsystem layouts, and sensor classifications demonstrate widely recognized automotive instrumentation and vehicle dynamics principles rather than specific proprietary vehicle designs.
- **Illustrative Parameters**: Sampling rates, operating boundaries, sensor response curves, and simulated fault modes are illustrative values designed for educational visualization and interactive experimentation.
- **Independent Educational Scope**: The application is an independent educational exploration of 3D computer graphics, user interface design, and simulated telemetry data paths in the browser.

