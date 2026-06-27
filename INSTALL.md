# SpecPine — Install Guide

RF spectrum analysis for the Hak5 WiFi Pineapple Pager via Wi-Spy DBx.

---

## What you need

- Hak5 WiFi Pineapple Pager (firmware 24.10.1)
- Wi-Spy DBx connected via USB
- `pine-spectools.zip` (from the GitHub releases page or built locally with `bash scripts/package.sh`)
- SSH/SCP access to the Pager (`root@172.16.42.1`, password `hak5pineapple`)
- Internet on the Pager for the first-run dependency install

---

## Install

### Step 1 — Copy the ZIP to the Pager

```bash
scp pine-spectools.zip root@172.16.42.1:/root/
```

### Step 2 — Bootstrap the installer payload

```bash
ssh root@172.16.42.1 '
  cd /tmp &&
  unzip -o /root/pine-spectools.zip "pine-spectools/specpine_installer/payload.sh" &&
  mkdir -p /root/payloads/user/utils/specpine_installer &&
  cp pine-spectools/specpine_installer/payload.sh \
     /root/payloads/user/utils/specpine_installer/payload.sh &&
  chmod 755 /root/payloads/user/utils/specpine_installer/payload.sh
'
```

### Step 3 — Run the installer from the Pager

On the Pager: **Payloads → utils → SpecPine Installer → Launch**

The installer will:

1. Check for `python3` and `evtest` — installs them via opkg if missing (needs internet, prompts once)
2. Extract `pine-spectools.zip` and copy SpecPine to `/root/payloads/user/reconnaissance/specpine/`
3. Set all file permissions

SpecPine then appears under **Payloads → reconnaissance → SpecPine**.

---

## Where files go

| Source in ZIP | Destination on Pager |
|---|---|
| `pine-spectools/specpine/` | `/root/payloads/user/reconnaissance/specpine/` |
| `specpine/bin/spectool_raw` | bundled MIPS binary — no system install needed |
| `specpine/lib/libusb-*.so*` | bundled — no system install needed |
| `specpine/bin/*.py` | Python helpers — need `python3` on the Pager |

---

## Updating

Drop a new `pine-spectools.zip` at `/root/pine-spectools.zip` via SCP, then run the **SpecPine Installer** payload again from the Pager UI.

---

## Usage

Plug in the Wi-Spy DBx, then launch **SpecPine** from the Pager.

The boot splash plays, then the main menu appears:

| Menu item | What it does |
|---|---|
| **2.4GHz Waterfall** | Full-colour waterfall on the display, 2.4 GHz band |
| **5GHz Waterfall** | Full-colour waterfall on the display, 5 GHz band |
| **NFO** | Device status, settings snapshot, and release info |

### Controls (during waterfall)

| Input | Action |
|---|---|
| Hold OK ≥ 0.8 s | Stop scan, return to menu |
| Hold Back ≥ 2 s | Exit SpecPine |
| Hold DOWN ≥ 2 s | Save screenshot to loot folder |

### Loot

Sessions are saved automatically to `/root/loot/specpine/session_YYYYMMDD_HHMMSS/`:

```
├── meta.json          # band, device, freq range, status
├── events.jsonl       # full JSONL sweep stream
├── sweep_summary.csv  # per-sweep min/max/avg dBm
└── screenshot_*.bmp   # framebuffer captures (if taken)
```

Retrieve over SSH:

```bash
scp -r root@172.16.42.1:/root/loot/specpine ./loot/
```

---

## Troubleshooting

**"spectool_raw not found"** — Re-run the installer payload. The binary ships in the ZIP and should already be at `bin/spectool_raw` inside the payload directory.

**"Bridge exited — check Wi-Spy USB"** — Wi-Spy not detected. Verify over SSH:

```bash
ssh root@172.16.42.1 "lsusb"
# expect: Bus ... ID 1781:... or 1dd5:... (MetaGeek)

PAYLOAD=/root/payloads/user/reconnaissance/specpine
ssh root@172.16.42.1 "LD_LIBRARY_PATH=${PAYLOAD}/lib ${PAYLOAD}/bin/spectool_raw --list"
```

**Blank screen after waterfall exits** — The virtual console should rebind automatically. If it doesn't:

```bash
ssh root@172.16.42.1 "echo 1 > /sys/class/vtconsole/vtcon1/bind"
```

**No tone on screenshot** — Check that ringtones are installed. On first launch SpecPine will prompt to install them. If you skipped it, re-launch and confirm when prompted.

**"HUD unavailable — falling back to standard menu" / `ModuleNotFoundError: No module named 'decimal'` (or `'urllib'`)** — The Pager's base firmware ships a bare `python3` binary, but `decimal` and `urllib` are separate opkg packages not included in `python3-light`, and not installed by default. SpecPine's first-run dependency check now test-imports the actual modules it needs and installs the right packages automatically (confirm the prompt when asked). If you installed manually over SSH and skipped that prompt, run:

```bash
ssh root@172.16.42.1 "opkg update && opkg install python3-light python3-decimal python3-urllib"
```

---

## Manual install (SSH only)

If you prefer not to use the installer payload:

```bash
sshpass -p hak5pineapple scp pine-spectools.zip root@172.16.42.1:/root/

sshpass -p hak5pineapple ssh root@172.16.42.1 '
  cd /tmp && unzip -o /root/pine-spectools.zip &&
  rm -rf /root/payloads/user/reconnaissance/specpine &&
  cp -r pine-spectools/specpine /root/payloads/user/reconnaissance/specpine &&
  chmod 755 /root/payloads/user/reconnaissance/specpine/payload.sh &&
  chmod 755 /root/payloads/user/reconnaissance/specpine/bin/*.py &&
  chmod 755 /root/payloads/user/reconnaissance/specpine/bin/spectool_raw &&
  chmod 755 /root/payloads/user/reconnaissance/specpine/bin/spectool_net
'
```

Then install dependencies on the Pager if not already present. The Pager firmware already ships a bare `python3` binary, but OpenWrt 24.10.1 splits the stdlib into separate opkg packages — `python3-light` alone does **not** include `decimal` or `urllib`, which SpecPine's Python helpers need transitively (`statistics` → `fractions` → `decimal`, `pathlib` → `urllib`). Install all three explicitly:

```bash
sshpass -p hak5pineapple ssh root@172.16.42.1 'opkg update && opkg install python3-light python3-decimal python3-urllib evtest'
```

(The in-app installer/dependency check does this automatically and verifies the imports actually work — see Troubleshooting below if you skip this manual step and hit a `ModuleNotFoundError`.)

---

## Rebuilding from source

```bash
bash scripts/package.sh
```

Produces `pine-spectools.zip` from `payloads/specpine/` and `spectools-pineapple-build/`. Cross-compiled MIPS binaries in `spectools-pineapple-build/bin/` only need rebuilding if you change the upstream `spectool sourcecode/`.
