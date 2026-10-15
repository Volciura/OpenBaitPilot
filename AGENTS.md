# Codex instructions — OpenBaitPilot

## Purpose
OpenBaitPilot is the STM32-based autopilot firmware for an RC bait boat. This repository owns real-time boat control, navigation, and fail-safe behavior.

## System boundaries
- **STM32 (this repository):** read RC inputs, operate propulsion/steering/servos, read onboard GPS, manage operating modes, waypoints, autonomous navigation and safety logic.
- **ESP32 (`OpenBaitPilotWiFiBridge`, separate repository):** communications gateway between STM32 and Android, plus sonar-related data transport/integration. It is not the navigation controller.
- **Android (`OpenBaitPilotAndroidApp`, separate repository):** map, UI, route/waypoint planning, telemetry display, and prospective sonar/bathymetry features.
- STM32 must remain capable of safe operation when Wi-Fi, ESP32, or Android is unavailable.

## Established project context
- Boat is approximately 80 cm long and can carry up to 2 kg of bait.
- RC transmitter: FlySky FS-i6; receiver: IA10B.
- ESC: Flycolor 120A; motor: 4250 800 KV; current propulsion battery: 3S. A 4S conversion was discussed but deferred.
- GPS is connected directly to STM32, not routed through ESP32.
- Intended three-position control: manual control / save point / autopilot. Exact channel mapping and mode transition semantics still need confirmation.
- The project aims to support waypoints, autonomous routes and eventually systematic sector scanning.
- Lighting and auxiliary control are potential firmware features; actual GPIO/power circuitry is not yet documented.

## Working rules
1. Read `docs/project-context.md` before proposing hardware-dependent changes.
2. Never invent a specific STM32 board, pinout, GPS module, electrical interface, protocol frame, or confirmed sensor model. Mark missing details as `TBD` and ask for confirmation.
3. Treat radio manual override, failsafe, loss of RC, loss of GPS, low voltage and loss of communications as safety-critical. Do not implement unreviewed autonomous motor behavior.
4. Keep control/navigation logic independent from the communications transport. Define explicit message types and versions before integrating with the ESP32 bridge.
5. Prefer nonblocking control loops, explicit state machines, bounded timeouts and reproducible bench tests.
6. Document assumptions, protocol changes and hardware dependencies. Update `docs/project-context.md` when a decision is confirmed.
7. Do not conflate speculative ideas with approved requirements.
8. Before hardware actuation tests, require a safe bench configuration (propeller removed or motor disconnected as appropriate).

## Suggested initial work
- Inspect the actual STM32 board and existing source tree when available.
- Confirm RC receiver interface, ESC/servo outputs, GPS interface, power rails and mode-switch channel.
- Design the operating-mode state machine and failsafe priority.
- Define and version a minimal STM32 ↔ ESP32 command/telemetry protocol.
- Implement/test manual RC pass-through before autonomous navigation.

## Related repositories (planned)
- `Volciura/OpenBaitPilotWiFiBridge`
- `Volciura/OpenBaitPilotAndroidApp`

These names describe the intended split; do not assume the related repositories already exist.
