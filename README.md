# INTELLIX ARM 1.0

**INTELLIX · Robotics & AI** — A browser-based 6-DOF pick-and-place robotic arm control and motion-sequencing interface built with React, TypeScript, Tailwind CSS, Zustand, Three.js (`@react-three/fiber`), and the Web Serial API.

No backend required. Runs entirely in the browser and communicates directly with Arduino or ESP32 microcontrollers over USB Serial.

---

## Features

- **Real-Time 3D Arm View**: Interactive Three.js 6-DOF kinematic arm (`VIEW IN 3D`) with OrbitControls, `GLASS` translucent shell mode, `RINGS` joint axis overlays, and X/Y/Z guide axes.
- **Manual Joint & Cartesian Control**:
  - 7 sliders (`Speed`, `Gripper`, `Wrist 1`, `Wrist 2`, `Elbow`, `Shoulder`, `Base`) with numeric inputs, per-joint lock toggles, and soft limits modal (`LIMITS`).
  - Analytical **Inverse Kinematics (`go to`)** for `(X, Y, Z)` mm targets and **`+ LINE`** Cartesian straight-line linear interpolation generator.
- **Multi-Track Motion Sequencer**:
  - 6 horizontal tracks with keyframe capture (`+ CAPTURE`), `UPDATE`, scrubbing blue playhead, marquee box select, duplicate, cut/paste, link/sync toggle, loop mode, zoom, and `linear` / `ease-in-out` interpolation streamed at ~50Hz.
- **Saved Poses & Autosave**:
  - Default `Home`, `Rest`, `Close gripper` poses with customizable transition durations and `+` save.
  - Undo/Redo history (`Ctrl+Z` / `Ctrl+Y`) and automatic `localStorage` session recovery.
- **Web Serial & Code Export**:
  - Direct USB Web Serial connection with selectable baud rate (default `115200`). Falls back seamlessly to `SIMULATION` mode when disconnected or in unsupported browsers.
  - JSON project Import/Export and one-click Arduino/ESP32 `.ino` sketch export (`</>`).
- **Multilingual & Themeable**: English (`EN`), Hindi (`HI`), Kannada (`KN`), Dark/Light mode, Fullscreen, and `E-STOP` (`Esc`).

---

## Setup & Run

### Prerequisites
- Node.js 18+ (verified with Node v24.14.0)
- Chrome or Edge (required for Web Serial hardware connection; Simulation mode works in any modern browser)

### Install & Start

```bash
npm install
npm run dev
```

Open the local URL printed by Vite (typically `http://localhost:5173`).

### Production Build

```bash
npm run build
```

---

## Connecting to Arduino / ESP32

1. Open [arduino/intellix_arm_controller/intellix_arm_controller.ino](arduino/intellix_arm_controller/intellix_arm_controller.ino) in the Arduino IDE.
2. **Servo Wiring (Direct PWM default)**:
   - **Base**: Pin `3` (Arduino) / GPIO `13` (ESP32)
   - **Shoulder**: Pin `5` / GPIO `12`
   - **Elbow**: Pin `6` / GPIO `14`
   - **Wrist 2**: Pin `9` / GPIO `27`
   - **Wrist 1**: Pin `10` / GPIO `26`
   - **Gripper**: Pin `11` / GPIO `25`
   - *(Optional PCA9685 I2C driver)*: Uncomment `#define USE_PCA9685` in the `.ino` sketch and install `Adafruit PWM Servo Driver Library`. Connect channels `0..5` in order `B, S, E, W2, W1, G`.
   - Always power servos from an external regulated 5V–6V supply with common GND shared with the Arduino/ESP32.
3. Flash the sketch to your board at `115200` baud and close the Arduino Serial Monitor.
4. In INTELLIX ARM, click **NOT CONNECTED**, choose `115200 bps`, click **Choose Port & Connect**, and select your microcontroller COM/USB port.

---

## Serial Protocol Customization

The outgoing ASCII protocol is isolated in [src/lib/serialProtocol.ts](src/lib/serialProtocol.ts).

Default frame format sent at up to 50Hz:

```text
B90,S90,E84,W2:76,W1:90,G90\n
```

Emergency stop frame:

```text
ESTOP\n
```

Edit `formatServoFrame`, `parseServoFeedback`, or `PROTOCOL_CONFIG` in `src/lib/serialProtocol.ts` to adapt to custom firmware.

---

## Keyboard Shortcuts

| Key | Action |
| --- | --- |
| `Space` | Play / Pause sequence |
| `C` | Capture keyframe at playhead |
| `Delete` / `Backspace` | Delete selected keyframe(s) |
| `ArrowLeft` / `ArrowRight` | Nudge selected keyframe(s) ±0.1s |
| `Ctrl+Z` / `Ctrl+Y` | Undo / Redo |
| `Esc` | Immediate **E-STOP** |
