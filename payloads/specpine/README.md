# SpecPine v1.0 Alpha

RF spectrum analysis for the **Hak5 WiFi Pineapple Pager** via **Wi-Spy DBx** USB analyzer.

Single self-contained payload — ships its own MIPS binaries, libusb, and Python helpers. No system-wide install required.

---

## Main menu

| Item | What it does |
|---|---|
| **2.4GHz Waterfall** | Full-colour RGB565 waterfall on `/dev/fb0`, 2.4 GHz band |
| **5GHz Waterfall** | Full-colour RGB565 waterfall on `/dev/fb0`, 5 GHz band |
| **NFO** | Device status, current config, and release credits |

---

## Controls (during waterfall)

| Input | Action |
|---|---|
| Hold OK ≥ 0.8 s | Stop scan, return to menu |
| Hold Back ≥ 2 s | Exit SpecPine |
| Hold DOWN ≥ 2 s | Save framebuffer screenshot to loot folder |

---

## Loot

Each scan session is saved to `/root/loot/specpine/session_<TS>/`:

```
session_YYYYMMDD_HHMMSS/
├── meta.json          # band, device, freq range, status
├── events.jsonl       # full bridge stream (device_config + sweep events)
├── sweep_summary.csv  # per-sweep min/max/avg dBm
└── screenshot_*.bmp   # framebuffer screenshots (if taken with DOWN hold)
```

Pull it home:
```bash
scp -r root@172.16.42.1:/root/loot/specpine ./loot/
```

**No-loot mode** stores sessions under `/tmp/specpine/` and wipes them on exit.

---

## Install

See `INSTALL.md` for the full walkthrough.

Quick version: SCP `pine-spectools.zip` to `/root/` on the Pager, bootstrap the installer payload via SSH, then run it from the Pager UI. The installer handles dependencies and file placement automatically.

---

## Data flow

```
Wi-Spy DBx (USB)
  → spectool_raw           (MIPS binary; emits sweep lines)
  → spectools_bridge.py    (parses native format → JSONL at /tmp/specpine_events.jsonl)
  → spectools_waterfall_fb.py   (RGB565 → /dev/fb0, 480×222 landscape, 6 FPS)
```

---

## Layout

```
specpine/
├── payload.sh              # orchestrator
├── include/
│   ├── funcs_main.sh       # device probe, session lifecycle, button watcher
│   ├── funcs_menu.sh       # menus, NFO screen, dependency install
│   └── funcs_scan.sh       # graphical_waterfall() — sole scan mode
├── bin/
│   ├── spectool_raw        # MIPS binary (mipsel_24kc)
│   ├── spectool_net
│   ├── spectools_bridge.py
│   ├── spectools_waterfall_fb.py
│   ├── specpine_hud.py     # custom framebuffer main menu
│   ├── specpine_splash.py  # boot animation
│   └── fb_screenshot.py    # framebuffer capture
├── lib/                    # libusb-0.1 + libusb-1.0 + symlinks
└── data/
    ├── 99-wispy.rules
    ├── ansi/               # per-mode CRT frames
    └── theme/              # palette, splash.fb, boot animation frames
```

---

## Credits

- **BluePine** (cncartist) — UX patterns, button-watcher, ringtone names
- **spectools** (caljorden, dragorn / kismet project) — spectrum driver code
- **Error404** — Pager port and SpecPine bundle
