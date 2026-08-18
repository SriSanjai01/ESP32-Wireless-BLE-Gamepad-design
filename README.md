# 🎮 ESP32 Wireless BLE Gaming Controller

A high-performance, low-latency custom wireless gaming controller built using an ESP32-DEVKITC-32E microcontroller. The system reads dual 2-axis analog joysticks and 15 tactile action & system buttons, transmitting human interface device (HID) controller inputs wirelessly over Bluetooth Low Energy (BLE) to PCs, smartphones, and consoles.

---

## 📌 Project Overview & Design Scope

> **Note:** This repository is focused strictly on **custom PCB hardware design, schematic capture, component library creation, and 2-layer PCB layout using Altium Designer**. It is tailored for hardware makers and PCB designers looking to build a clean custom controller board without needing to study deep microcontroller internal architecture.

This project combines dual 2-axis potentiometric joysticks, 15 tactile pushbuttons, and wireless Bluetooth HID communication onto a custom controller PCB. It is powered directly via USB or a 3.7V rechargeable LiPo battery, providing a complete standalone gamepad solution with zero external resistor requirements by utilizing the ESP32's internal software pull-up architecture (`INPUT_PULLUP`).

---

## 🚀 Key Features

- **📶 Wireless BLE Gamepad HID:** Native Bluetooth Low Energy HID profile recognized natively by Windows, Android, iOS, Linux, and Steam without external drivers.
- **🕹️ Dual 2-Axis Analog Joysticks:** Uses dedicated **ADC1 channels** (`GPIO 36`, `39`, `32`, `33`) to ensure zero signal interference while Bluetooth transmission is active.
- **🎯 17 Digital Inputs:** Full gamepad layout including D-Pad (UP, DOWN, LEFT, RIGHT), Action Buttons ($\triangle$, $\square$, $\bigcirc$, $\times$), Shoulders/Triggers (L1, L2, R1, R2), System Buttons (START, SELECT, HOME), and Stick Switches (L3, R3).
- **⚡ Zero External Resistor PCB Design:** All digital inputs switch directly to Ground (`GND`), eliminating the need for external pull-up resistors on the board.
- **🔌 Zero-Crossover PCB Trace Layout:** Spatially grouped pin mapping designed to route all left-side components to Header J2 and right-side components to Header J3 with clean, parallel 2-layer traces in Altium Designer.

---

## 🧩 Hardware Components Used

- **Microcontroller Board:** ESP32-DEVKITC-32E Development Board (38-pin DIP package, U3)
- **Analog Joysticks:** COM-09032 / ALPS RKJXV 2-Axis Analog Joystick Modules with integrated push switch (U1, U2)
- **Tactile Switches:** 6x6x5mm 2-pin Through-Hole Pushbuttons (SW1–SW15)
- **Power & Charging:** TP4056 USB-C LiPo Charger Module & AP2112K-3.3V Low-Dropout Voltage Regulator
- **Battery:** 3.7V 1200mAh 1S LiPo Rechargeable Battery Cell

---

## ⚙️ How It Works

1. **Power:** The controller is powered by a 3.7V LiPo battery stepped down to a clean, low-noise 3.3V rail by the AP2112K LDO regulator (or USB 5V via ESP32 `EXT_5V`).
2. **Sensing:**
   - **Joysticks:** The Left Joystick (U1) sends analog voltage levels to ESP32 Pins `J2 3` (`SENSOR_VP`) and `J2 4` (`SENSOR_VN`). The Right Joystick (U2) sends voltage levels to Pins `J2 7` (`IO32`) and `J2 8` (`IO33`).
   - **Buttons:** Pressing any tactile button shorts the corresponding ESP32 GPIO pin directly to Ground (`GND_J2_14` / `GND_J3_1`), pulling the signal LOW.
3. **Action:**
   - The ESP32 continuously polls the inputs, maps 12-bit analog voltages (`0`–`4095`) to 16-bit gamepad axis values (`0`–`32767`), and transmits HID reports over Bluetooth LE at a 100Hz update rate.

---

## 🔌 Exact Circuit Schematic Pin Connections

This table matches your exact schematic design (`controller.SchDoc`):

| ESP32-DEVKITC-32E Symbol Pin (U3) | Pin Label on U3 | Connected Net Label | Target Module / Component Pin | Circuit Function |
| :--- | :--- | :--- | :--- | :--- |
| **J2 3** | `SENSOR_VP` | `SENSOR_VP` | U1 Left Joystick Pin H2 | Left Stick X-Axis Analog Input (ADC1_0) |
| **J2 4** | `SENSOR_VN` | `SENSOR_VN` | U1 Left Joystick Pin V2 | Left Stick Y-Axis Analog Input (ADC1_3) |
| **J2 5** | `IO34` | `IO34` | U1 Left Joystick Pin B1A (L3 Switch) | Left Stick Pushbutton Input |
| **J2 6** | `IO35` | `START` | START Button Pin 1 | Start System Button Input |
| **J2 7** | `IO32` | `IO32` | U2 Right Joystick Pin H2 | Right Stick X-Axis Analog Input (ADC1_4) |
| **J2 8** | `IO33` | `IO33` | U2 Right Joystick Pin V2 | Right Stick Y-Axis Analog Input (ADC1_5) |
| **J2 9** | `IO25` | `L2` | L2 Button Pin 1 | Left Trigger Button Input |
| **J2 10** | `IO26` | `L1` | L1 Button Pin 1 | Left Bumper Button Input |
| **J2 11** | `IO27` | `UP` | D-Pad UP Button Pin 1 | D-Pad Up Directional Input |
| **J2 12** | `IO14` | `LEFT` | D-Pad LEFT Button Pin 1 | D-Pad Left Directional Input |
| **J2 13** | `IO12` | `RIGHT` | D-Pad RIGHT Button Pin 1 | D-Pad Right Directional Input |
| **J2 14** | `GND_J2_14` | `GND` | U1/U2 V3, H3, B2A, S1–S4 & Button Pin 2s | Main Logic Ground Rail |
| **J2 15** | `IO13` | `DOWN` | D-Pad DOWN Button Pin 1 | D-Pad Down Directional Input |
| **J3 1** | `GND_J3_1` | `GND` | Common Ground Rail | Main Logic Ground Rail |
| **J3 2** | `IO23` | `HOME` | HOME Button Pin 1 | System Home/PS Button Input |
| **J3 3** | `IO22` | `R2` | R2 Button Pin 1 | Right Trigger Button Input |
| **J3 6** | `IO21` | `R1` | R1 Button Pin 1 | Right Bumper Button Input |
| **J3 7** | `GND_J3_7` | `GND` | Common Ground Rail | Main Logic Ground Rail |
| **J3 8** | `IO19` | `TRIANGLE` | TRIANGLE Button Pin 1 | Action Button ($\triangle$) Input |
| **J3 9** | `IO18` | `SQUARE` | SQUARE Button Pin 1 | Action Button ($\square$) Input |
| **J3 10** | `IO5` | `CIRCLE` | CIRCLE Button Pin 1 | Action Button ($\bigcirc$) Input |
| **J3 11** | `IO17` | `CROSS` | CROSS Button Pin 1 | Action Button ($\times$) Input |
| **J3 12** | `IO16` | `SELECT` | SELECT Button Pin 1 | Select System Button Input |
| **J3 13** | `IO4` | `IO4` | U2 Right Joystick Pin B1A (R3 Switch) | Right Stick Pushbutton Input |

---

## 🚧 Challenges Faced & Solutions

### 1. Custom Integrated Library Creation (.IntLib / .LibPkg)
- **Challenge:** Standard EDA libraries lacked accurate footprints and 3D CAD models for the **COM-09032 2-Axis Analog Joystick** module and the **ESP32-DEVKITC-32E** DIP module board.
- **Solution:** Built custom Schematic Symbol Libraries (`.SchLib`) from scratch following manufacturer datasheets, created exact PCB Footprint Libraries (`.PcbLib`) with IPC pad specifications, imported high-resolution 3D STEP models, aligned 3D body origins to the footprint pads, and compiled a unified Integrated Library (`.IntLib`).

### 2. Elimination of PCB Rat's Nest Crossovers
- **Challenge:** Default pin assignments caused signal wires to cross over the ESP32 module from left to right, creating a dense crossover mesh that prevented clean routing on a 2-layer PCB.
- **Solution:** Redesigned the schematic pinout using **Spatial Grouping**: all left-hand controls (D-Pad, Left Joystick, L1, L2, START) route strictly to Left Header J2, while right-hand controls (Action buttons, R1, R2, SELECT, HOME) route to Right Header J3. This allowed 100% parallel trace routing without crossovers in Altium Designer.

---
