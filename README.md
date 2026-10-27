# LEGO Motor Control (Web Bluetooth)

Single-file web app to drive LEGO Powered Up / Control+ hub motors from Chrome or Edge (Windows, Android).
Built only from LEGO's official [LEGO Wireless Protocol 3.0 docs](https://lego.github.io/lego-ble-wireless-protocol-docs/).

**Live:** https://bannonbotboss.github.io/lego-motor-control/

Features: connect, auto-detected ports (Hub Attached I/O), speed slider, float/brake, go-to-angle, set zero, battery + position readouts, STOP ALL, keyboard shortcuts, raw TX/RX hex log.

Keys: Space = stop all · 1–4 select port A–D · ↑/↓ speed ±10 · F float · B brake · Z set zero.

## Byte cheat sheet (LWP3)
BLE service `00001623-1212-efde-1623-785feabcd123`, characteristic `00001624-1212-efde-1623-785feabcd123`.
Every message: `[length, 0x00 hubId, msgType, ...]`. Multi-byte values little-endian. Ports: A=0, B=1, C=2, D=3.

| Action | Bytes (port A) | Spec section |
|---|---|---|
| Battery % updates on | `05 00 01 06 02` | Hub Properties (0x01), prop 0x06, op Enable Updates |
| Battery reply | `06 00 01 06 06 <pct>` | Hub Properties Update |
| Port attached (from hub) | `0F 00 04 <port> 01 <typeLo> <typeHi> ...` | Hub Attached I/O (0x04) |
| StartSpeed 50% | `09 00 81 00 11 07 32 64 00` | Port Output Cmd 0x81, sub 0x07 (speed int8, maxPower, profile) |
| Reverse 50% | `09 00 81 00 11 07 CE 64 00` | speed −50 = 0xCE |
| Float | `09 00 81 00 11 07 00 64 00` | speed 0 = float |
| Brake | `09 00 81 00 11 07 7F 64 00` | speed 127 = brake |
| Go to 90° @50%, hold | `0E 00 81 00 11 0D 5A 00 00 00 32 64 7E 00` | sub 0x0D GotoAbsolutePosition (int32 deg, speed, power, endState 0/126/127, profile) |
| Set zero (PresetEncoder) | `0B 00 81 00 11 51 02 00 00 00 00` | sub 0x51 WriteDirectModeData, mode 2, int32 |
| Subscribe position | `0A 00 41 00 02 01 00 00 00 01` | Port Input Format Setup (0x41), mode 2, delta 1, notify |
| Position value (from hub) | `08 00 45 00 <int32 deg>` | Port Value Single (0x45) |

Startup/completion byte `0x11` = execute immediately + request feedback.
No LEGO app binaries were used. Not affiliated with LEGO.
