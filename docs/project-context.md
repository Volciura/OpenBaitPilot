# OpenBaitPilot — project context and decision log

_Last assembled: 2026-10-15. This document distinguishes decisions from ideas that require verification._

## 1. Goal
Build an STM32-based autopilot for a bait boat, retaining RC control and adding waypoint navigation. A separate ESP32 bridge and Android application will handle connectivity, maps and sonar-related functionality.

## 2. Architecture — confirmed direction
| Subsystem | Responsibility | Repository |
| --- | --- | --- |
| STM32 | RC input, navigation, GPS, control outputs, safety | `OpenBaitPilot` |
| ESP32 on boat | Communications and sonar bridge | `OpenBaitPilotWiFiBridge` (planned) |
| Android | UI, mapping, waypoint/route planning, telemetry | `OpenBaitPilotAndroidApp` (planned) |

**Key decision:** GPS connects to STM32. ESP32 is not the autopilot. STM32 must handle loss of Wi-Fi/Android safely.

## 3. Known hardware
| Item | Known information | Status |
| --- | --- | --- |
| Hull | Approx. 80 cm; payload up to 2 kg | Reported |
| RC transmitter | FlySky FS-i6 | Reported |
| RC receiver | FlySky IA10B | Reported |
| Brushless motor | 4250, 800 KV | Reported |
| ESC | Flycolor 120 A | Reported |
| Propulsion battery | 3S | Current choice |
| 4S upgrade | Considered, not planned now | Deferred |
| STM32 board | Exact part/board/pinout | TBD |
| GPS module | Exact model, baud rate, update rate, voltage | TBD |
| Steering actuator and servo count | Exact wiring and mechanics | TBD |
| ESP32 board | Exact model and interfaces for this build | TBD |
| Power regulation | Rails, voltage limits, wiring and protection | TBD |

Do not infer electrical compatibility from a component's name alone.

## 4. User interaction and control
A three-position transmitter switch is intended to select:
1. Manual RC operation.
2. Save current GPS waypoint.
3. Autopilot operation.

**To confirm:** which FS-i6 channel/switch is used; whether saving a point is edge-triggered; how to select target points; manual override priority; behavior on GPS or RC loss.

## 5. Navigation roadmap
- GPS navigation and saved waypoints.
- Autonomous travel to selected waypoints.
- Return-home behavior (policy and triggers TBD).
- Later: automated scanning of a selected water sector to gather sonar data.

Control algorithms, waypoint tolerances, speed limits, heading source and obstacle-handling policy are not yet approved.

## 6. Interfaces and safety
- Prefer a defined, versioned STM32 ↔ ESP32 interface (UART is a proposal, **not yet a confirmed hardware decision**).
- The bridge may forward commands and telemetry, but cannot be required for basic manual or safety functions.
- Safety scenarios to design: RC signal loss, GPS invalid/stale, bridge loss, low battery, startup/reset, mode switching and operator takeover.
- No motor-output code should be treated as field-ready without bench and controlled-water testing.

## 7. Related sonar and mapping work
- Kogger BASIC_2D-Sonar+ is the intended initial sonar purchase; side/down-scan may be added later.
- Accuphy/Router One and protocol conversion have been explored but are not adopted STM32 requirements.
- Android mapping and bathymetry belong in the Android/bridge projects; STM32 should expose navigation and status data without depending on any sonar brand.
- The practical radio/network range target discussed for the system is about 150 m; this is a goal, not a measured guarantee.

## 8. Decisions and non-decisions
### Confirmed or currently chosen
- Three separate repositories and firmware/app responsibilities.
- GPS directly on STM32.
- FlySky FS-i6 + IA10B RC system.
- Existing 3S propulsion setup retained for now.

### Proposed / requires confirmation
- UART between STM32 and ESP32.
- Specific state machine, failsafe rules, protocol format and packet rates.
- Lighting outputs and control scheme.
- Automatic sector-scanning algorithm.
- Exact STM32, GPS and ESP32 board selections and wiring.

## 9. Next questions to resolve
1. Exact STM32 board/MCU and firmware framework/toolchain.
2. Pin map: RC channels, ESC, servos, GPS, auxiliary outputs.
3. GPS module and available heading information.
4. Desired failsafe behavior in each mode.
5. STM32 ↔ ESP32 physical interface and message schema.
6. Current repository code and existing functionality, if any.

## 10. Documentation discipline
When a decision is made, move it from the proposed list into confirmed decisions, record the rationale and update related repositories' interface specifications. Avoid assuming that earlier brainstorming is a final design.
