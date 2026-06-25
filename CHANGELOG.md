# Changelog

All notable changes to IC-7300MK2 WebOp are documented here. This project
adheres to [Semantic Versioning](https://semver.org).

## [Unreleased]

### Added
- Over LAN, the radio's **MOD Input is set to LAN automatically on connect** (and
  restored on disconnect), so the browser mic transmits without changing the
  radio's menu. The LAN MOD level is bumped if it was 0.

### Changed
- **PTT is now tap-to-toggle** (was press-and-hold) so it works on touchscreens:
  tap to key TX, tap again for RX. Latched TX auto-releases for safety on
  screen-lock / app-switch, page close, a 5-minute backstop, and (server-side)
  if the last client disconnects.
- Microphone (TX) now shows a clear "needs HTTPS" message on insecure
  (plain-HTTP) connections instead of a generic "access denied" — browsers only
  expose `getUserMedia` in a secure context (HTTPS or localhost).

## [0.1.0] — 2026-06-25

First release. Browser-based CI-V control panel for the Icom IC-7300MK2
(HF / 50 MHz) with a live spectrum scope + waterfall. Sibling of
[icom-webop](https://github.com/Dreikor17/icom-webop) (IC-9700), kept separate.

### Added
- **Live spectrum scope + waterfall** decoded from CI-V `27 00` (475 points,
  0–160), with the tuned-channel marker and the receive filter passband shaded
  over it.
- **Transports** behind one interface:
  - **USB** serial CI-V (the MK2's USB-C virtual COM ports).
  - **LAN** — new on the MK2: Icom RS-BA1 UDP protocol (control/serial/audio on
    50001/50002/50003), shared with the IC-9700 implementation.
  - **Simulator** that speaks CI-V back and auto-connects on load.
- **Control** — HF/6 m bands (160 → 6 m), modes (LSB/USB/CW/CW-R/RTTY/RTTY-R/
  AM/FM), filter (FIL1–3), VFO A/B/A=B/swap. CI-V address `B6h`.
- **Tuning** — draggable main dial, mouse-wheel, click-to-tune on the scope,
  selectable step, and direct frequency entry.
- **Levels** — AF / RF / SQL / RF power with value readouts (power shown as %);
  RF power defaults to 0% on connect.
- **Audio over LAN** — RX audio playback + mic TX (16 kHz / 16-bit mono PCM).
- Real-time **S-meter** and frequency / mode / filter readouts.
- **Remembers** the chosen connection (and LAN IP / user / password).
- **Mobile-friendly** responsive layout with touch-sized controls.
- Binds to `0.0.0.0` by default (reachable over LAN / Tailscale).

### Notes
- Adapted from the IC-9700 app; the scope format and RS-BA1 LAN protocol are
  identical, so that code is shared. Verified end-to-end with the built-in
  simulator; not yet tested against IC-7300MK2 hardware.
- No authentication yet — restrict the bind interface and/or use Tailscale ACLs.
- Mic capture (TX) needs a secure context (HTTPS or localhost); RX audio works
  over plain HTTP.

[0.1.0]: https://github.com/Dreikor17/ic7300mk2-webop/releases/tag/v0.1.0
