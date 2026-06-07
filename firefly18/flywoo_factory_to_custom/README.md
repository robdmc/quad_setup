# firefly18 — factory → custom

Pilot preferences ported onto the firefly18 (`FLYWOOF405S_AIO`, Betaflight 4.5.1,
DJI O4 / HD video).

## Source

All settings copied from the **firefly20pro** snapshot dated **2026-05-14**
(`firefly20pro/2026-05-14/manual_diff.txt`). That quad is the same board
(`FLYWOOF405S_AIO`) and same pilot, so switches / rates / OSD layout transfer
cleanly. No other quads were used.

## What was ported

| File             | Contents                                                        |
|------------------|-----------------------------------------------------------------|
| `switches.txt`   | `aux` mode mappings — ARM, ANGLE, BEEPER, TURTLE, PREARM        |
| `rates_diff.txt` | Betaflight rate curve (rc 100/100/90, expo 30, srate 80)        |
| `osd_diff.txt`   | HD/MSP OSD element layout + units/alarms                        |
| `all.txt`        | All of the above wrapped in `batch start/end` + `save`          |

## OSD canvas (important)

The firefly18 factory runs OSD canvas **30×13** (`displayport AUTO`, `vcd AUTO`);
the firefly20pro runs **53×20** (`displayport MSP`, `vcd HD`) for its DJI O4 air
unit. OSD element position values are encoded relative to the canvas, so
`osd_diff.txt` / `all.txt` switch the firefly18 to **53×20 / MSP / HD** *and then*
apply the firefly20pro's positions. Changing the canvas is required — applying the
positions without it would scatter the elements.

## GPS note (important)

The firefly18 factory config has **no GPS** (no `feature GPS`, no GPS UART), and
the firefly20pro 5/14 config has no GPS *mode* switch either. Accordingly:

- No GPS RESCUE switch is included (there wasn't one in the 5/14 source).
- The GPS-dependent OSD elements present in the 5/14 source were **removed**
  (`osd_gps_speed`, `osd_gps_lon`, `osd_gps_lat`, `osd_gps_sats`,
  `osd_home_dir`, `osd_home_dist`). Leaving them would just show dead elements.

If you later add a GPS module to the firefly18, re-add those elements and the
`feature GPS` / GPS-UART / `gps_rescue_*` settings manually.

## Other things NOT ported (board/tune specific — review before touching)

- PIDs / filters / `feature` / `serial` / `resource` — these stay board-specific.
- Throttle curve (`thr_mid` / `thr_expo`): the firefly18 factory has its own
  (70 / 40); the 5/14 source leaves them at default, so pasting these files does
  **not** change them.
- Rates were stored under `rateprofile 2` on the firefly20pro; here they target
  `rateprofile 0` (the firefly18's active profile). Switch profiles first if you
  want them somewhere else.

## How to apply

Connect the firefly18 in Betaflight Configurator, open the CLI, and paste the
contents of `all.txt` (or paste each file individually). It ends with `save`,
which writes to flash and reboots. Verify modes in the **Modes** tab afterward.

> These are settings to *apply*; they have not yet been captured back from the
> firefly18 hardware. Once flown and confirmed, capture a real `manual_diff.txt`
> / `manual_dump.txt` snapshot under a dated directory.
