# IC-7300MK2 WebOp

A browser-based control panel for the **Icom IC-7300MK2** (HF / 50 MHz). It
recreates the radio's front panel — button-for-button band, mode, filter and
VFO control, a tuning dial, level controls — and renders a live **spectrum scope
+ waterfall** with the tuned-channel marker and receive filter passband drawn
over it, just like the radio's own display.

It ships with a built-in **simulator**, so the full interface and waterfall run
with no radio attached. Connect to a real radio by USB or LAN when you're ready.

> This is the IC-7300MK2 sibling of [icom-webop](https://github.com/Dreikor17/icom-webop)
> (IC-9700). The two are kept as separate programs.

## Features

- **Live bandscope + waterfall** decoded from CI-V `27 00` scope data (475
  points, range 0–160), with the tuned-frequency marker and the receive
  passband shaded over it.
- **Button-for-button control** — HF/6 m bands (160 → 6 m), modes (LSB/USB/CW/
  CW-R/RTTY/RTTY-R/AM/FM), filters (FIL1–3), VFO A/B/A=B/swap.
- **Tuning** — draggable main dial, mouse-wheel tuning, click-to-tune on the
  scope, selectable tuning step, and direct frequency entry.
- **Live readouts** — frequency, mode, filter, and a real-time S-meter.
- **Levels** — AF, RF, SQL and RF power (shown as %); power defaults to 0% at
  connect.
- **Audio over LAN** — RX audio plays in the browser; mic streams to the radio
  for TX (16 kHz / 16-bit mono PCM).
- **Remembers** the chosen connection (and LAN IP / user / password) in the
  browser.
- **Simulator** built in for development and demos with no hardware.

## How it talks to the radio

Control uses Icom's **CI-V** protocol (the official *IC-7300MK2 CI-V Reference
Guide* is the source of truth). The MK2's default CI-V address is **`B6h`**.
Two transports are supported behind one interface:

- **USB** — the MK2's USB-C port presents virtual COM ports for CI-V.
- **LAN (network)** — new on the MK2: an Ethernet port speaking Icom's RS-BA1
  UDP protocol (control 50001 / CI-V 50002 / audio 50003). The CI-V tunnel over
  the network is byte-identical to USB, so control + scope/waterfall + audio all
  work over LAN.

## Run it (Windows)

```
pip install -r requirements.txt
run.bat
```

Then open <http://localhost:8701>. It auto-starts the **Simulator** so the
waterfall is live immediately.

**USB:** set the radio's CI-V on the USB-C port, pick the COM port and baud,
Connect. **LAN:** on the radio set **Network Function = ON**, a network user +
password, leave it on/standby, then choose **LAN**, enter the IP/user/password,
Connect.

## Architecture

```
backend/
  civ.py        CI-V protocol: framing, BCD freq/levels, mode codes, 27 00 scope
  transport.py  SerialTransport (USB COM) + SimTransport (synthetic radio)
  lan.py        LanTransport — Icom RS-BA1 UDP (control/serial/audio + login)
  radio.py      decodes the stream into live state + sweeps; button actions
  server.py     Starlette/uvicorn — REST connect/ports + WebSocket (state + scope + audio)
frontend/       Icom-themed panel, scrolling waterfall, full control set
```

The transport interface means the simulator and a real radio are
interchangeable — everything above `transport.py` is identical for both.

## Notes

This is an independent project, not affiliated with or endorsed by Icom. The
LAN protocol is a clean-room implementation informed by the open-source
[wfview](https://gitlab.com/eliggett/wfview) and
[kappanhang](https://github.com/nonoo/kappanhang). Icom's copyrighted CI-V
Reference Guide PDF is not redistributed here — download it from Icom (see
`docs/README.md`).
