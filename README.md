```
 ███████╗██████╗ ███████╗ ██████╗██████╗ ██╗███╗   ██╗███████╗
 ██╔════╝██╔══██╗██╔════╝██╔════╝██╔══██╗██║████╗  ██║██╔════╝
 ███████╗██████╔╝█████╗  ██║     ██████╔╝██║██╔██╗ ██║█████╗
 ╚════██║██╔═══╝ ██╔══╝  ██║     ██╔═══╝ ██║██║╚██╗██║██╔══╝
 ███████║██║     ███████╗╚██████╗██║     ██║██║ ╚████║███████╗
 ╚══════╝╚═╝     ╚══════╝ ╚═════╝╚═╝     ╚═╝╚═╝  ╚═══╝╚══════╝

                    SPECTRUM WATERFALL ANALYZER v1.0
                   WiFi Signal Mapping for Pineapple Pager

                              by Error404

                              ~* RELEASES *~

                              MIT LICENSE

                         [ DEFINITELY BUGGY EDITION ]
                       [ LOOT SAVING CAPABILITIES INTACT ]


────────────────────────────────────────────────────────────────────────────
 WHAT IS THIS
────────────────────────────────────────────────────────────────────────────

SpecPine is a 2.4GHz and 5GHz WiFi spectrum waterfall analyzer built for the
Hak5 Pineapple Pager. Real-time RF visualization with full loot capturing.

Works with:
  ► 2.4GHz ISM Band scanning & visualization
  ► 5GHz UNII Band coverage mapping
  ► Live spectrum waterfall display
  ► Signal strength correlation, (Mapping in future)

⚠ WARNING: This tool is buggy AF and still in active dev. Crashes happen.


────────────────────────────────────────────────────────────────────────────
 FEATURES (some of which actually work)
────────────────────────────────────────────────────────────────────────────

  ✓ Real-time spectrum waterfall (2.4 & 5GHz bands)
  ✓ Live RF signal strength visualization
  ⚠ weird UI slowness, and navigation
  ⚠ Memory leak on long captures (yeah I know)
  ⚠ Screen-shoting delays, and crashes occasionally.
  ✓ Saves your loot even when it crashes


────────────────────────────────────────────────────────────────────────────
 SCREENSHOTS
────────────────────────────────────────────────────────────────────────────

![Spectrum Waterfall 2.4GHz]((https://raw.githubusercontent.com/Error404-net/SpecPine/refs/heads/main/specpine-Sample-loot/session_20260627_084937_fb/screenshot_20260627_085159_1.bmp))
*2.4GHz band spectrum visualization in full swing*

![Menu](https://raw.githubusercontent.com/Error404-net/SpecPine/refs/heads/main/Screenshots/main_menu.png)
*Main Menu


────────────────────────────────────────────────────────────────────────────
 INSTALLATION
────────────────────────────────────────────────────────────────────────────

On Hak5 Pineapple:

  $ git clone https://github.com/Error404-net/SpecPine.git
  $ cd SpecPine
  $ ./install.sh

Or manual (if you're into pain):

  $ pip install -r requirements.txt
  $ python specpine.py


────────────────────────────────────────────────────────────────────────────
 REQUIREMENTS
────────────────────────────────────────────────────────────────────────────

  • Python 3.7+
  • libpcap
  • airmon-ng (for monitor mode)
  • numpy / matplotlib
  • scapy (packet handling)
  • Pineapple hardware (or Linux box with compatible WiFi card)


────────────────────────────────────────────────────────────────────────────
 KNOWN BUGS
────────────────────────────────────────────────────────────────────────────

  ⚠ Band switching causes segfault (use restart as workaround)
  ⚠ Memory usage climbs constantly on capture
  ⚠ 5GHz detection misses DFS channels sometimes
  ⚠ Waterfall colors invert on certain Pineapple firmwares
  ✓ HOWEVER: Loot is saved BEFORE crashes happen (feature not bug)


────────────────────────────────────────────────────────────────────────────
 CONFIG
────────────────────────────────────────────────────────────────────────────

Edit specpine.cfg to tune:

  [capture]
  interface = wlan0
  band = 2.4
  channel_hop = true
  hop_interval = 1000  ; milliseconds

  [output]
  loot_dir = ./loot
  format = pcap
  auto_save = true

  [display]
  waterfall_height = 800
  update_rate = 60  ; fps (capped)


────────────────────────────────────────────────────────────────────────────
 LOOT SAVING
────────────────────────────────────────────────────────────────────────────

Your captured handshakes, ESSIDs, and packets are automatically dumped to:

  ./loot/[timestamp]/

Even if the app crashes mid-capture, you're not losing that WPA2 handshake.
The loot thread writes independently. This is not accidental design, this is
tactical redundancy.


────────────────────────────────────────────────────────────────────────────
 LICENSE
────────────────────────────────────────────────────────────────────────────

MIT License. Do what you want with it.


────────────────────────────────────────────────────────────────────────────
Credits
────────────────────────────────────────────────────────────────────────────

spectools / caljorden
spectools / dragorn

────────────────────────────────────────────────────────────────────────────
 SUPPORT / BUGS
────────────────────────────────────────────────────────────────────────────

GitHub: https://github.com/Error404-net/SpecPine
Issues: Known.

This was written with caffeine, and zero regards for stability.
If it works, great. If it doesn't, ask claude to "Make no mistakes"

Questions? Open an issue. I might even fix it.


════════════════════════════════════════════════════════════════════════════

                           ~ ENJOY YOUR LOOT ~

════════════════════════════════════════════════════════════════════════════
```
