# SpecPine

RF spectrum analysis for the **Hak5 WiFi Pineapple Pager**, using a **MetaGeek Wi-Spy DBx** USB spectrum analyzer.

SpecPine is a single, self-contained Pager payload — it ships its own cross-compiled MIPS binaries, libusb, Python helpers, framebuffer renderer, and boot splash. No system-wide install is required to run it.

This repo also contains the upstream `spectools` C sources (the original Linux/GTK spectrum analyzer for the Wi-Spy DBx) and the cross-compiled `mipsel_24kc` drop used to feed the payload.

---

## What it does

Plug a Wi-Spy DBx into the Pager's USB port, launch SpecPine, and get a live full-colour waterfall of the 2.4 GHz or 5 GHz band rendered straight to the Pager's display — plus a device/status screen and persistent settings.

| Main menu item | What it does |
|---|---|
| **2.4GHz Waterfall** | Live RGB565 waterfall, 2.4 GHz band |
| **5GHz Waterfall** | Live RGB565 waterfall, 5 GHz band |
| **NFO** | Hardware status, current config, release credits |

Controls during a scan: tap OK to pause, hold OK to stop, hold Back to exit, hold Down to save a framebuffer screenshot to loot.

See `payloads/specpine/README.md` for the full menu/controls/loot reference, and `INSTALL.md` for setup.

---

## Quick start

```bash
bash scripts/package.sh   # builds pine-spectools.zip
```

Then follow `INSTALL.md` to copy it onto the Pager. First launch installs `python3` stdlib pieces and `evtest` via opkg if missing (needs the Pager on the internet, prompts once).

---

## Repository layout

```
payloads/specpine/          # the shipped payload — source of truth, see its own README.md
  payload.sh                 # orchestrator
  include/                   # funcs_main.sh, funcs_menu.sh, funcs_scan.sh
  bin/                        # spectool_raw, spectool_net, Python bridge + renderers + HUD
  data/                       # ASCII logo, ANSI frames, udev rules, theme assets
  lib/                         # libusb-0.1 + libusb-1.0 (populated at package time)

payloads/legacy/             # prior 3-payload split (install/waterfall/graphical) — reference only, not shipped

spectool sourcecode/         # upstream spectools C sources (kismet project)
spectools-pineapple-build/   # pre-cross-compiled MIPS spectool_raw/spectool_net + libusb — build artifact, read-only

scripts/
  package.sh                  # builds dist/pine-spectools/ and pine-spectools.zip
  generate_theme_images.py    # regenerates repo promo art in images/specpine/

dist/pine-spectools/         # staging area rebuilt entirely by package.sh — don't edit directly
images/specpine/             # README/promo art only, not shipped in the zip
```

---

## Building the Wi-Spy toolchain drop

Only needed when updating `spectool sourcecode/`. Requires the OpenWrt 24.10.1 SDK for `mipsel_24kc`:

```bash
cd "spectool sourcecode"
./configure --host=mipsel-openwrt-linux-musl --prefix=/usr --disable-gtk --disable-curses
make -j$(nproc)
strip spectool_raw spectool_net
```

The resulting binaries go into `spectools-pineapple-build/{bin,lib}/`, which `scripts/package.sh` reads from when building the payload zip.

---

## Credits

- **BluePine** (cncartist) — UX patterns, button-watcher pattern, ringtone names that SpecPine's menu/dialog code is adapted from
- **spectools** (caljorden, dragorn / kismet project) — the original Wi-Spy spectrum analyzer driver and tools
- **Error404** — Pager port and SpecPine bundle

## License

See upstream `spectool sourcecode/` for the original spectools license. SpecPine's Pager-specific payload code (`payloads/specpine/`) is provided as-is for use with the Hak5 WiFi Pineapple Pager.
