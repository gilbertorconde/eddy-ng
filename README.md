# eddy-ng

> ***Note: October 2025 -- life has gotten quite busy lately, so I've been much slower to respond to issues and make updates. Apologies, will get back to it soon!***

eddy-ng improves the Eddy current probe support in Klipper to add accurate Z-offset setting by physically making contact with the build surface. These probes are very accurate, but suffer from drifts due to changes in conductivity in the target surface as well as changes in coil parameters as temperatures change. Instead of doing temperature compensation (which is guesswork at best), eddy-ng takes a more physical approach:

1. Calibration is performed at any temperature (cold).
2. Z-homing via the sensor happens using this calibration, regardless of current temperatures. This is a "coarse" Z-home -- it is not accurate enough for printing, but is sufficient for homing, gantry leveling, and other preparation.
3. A precise Z-offset is taken with a "tap" just before printing, with the bed at print temps and the nozzle warm (but not hot -- you don't want filament drooling or damage to your build plate).
4. At the same time as the tap, the difference between the actual height (now known after the tap) and what the sensor reads at that height is saved. This offset then gets taken into account when doing a bed mesh, because it indicates the delta (due to temperatures) between what height the sensor thinks it is vs. where it actually is.

This is a standalone `eddy-ng` repository, intended to be integrated into your own Klipper installation.

## Calibration workflow

`eddy-ng` now treats normal homing calibration and tap calibration differently:

- **Normal calibration** (run via `PROBE_EDDY_NG_SETUP` for auto-selection or `PROBE_EDDY_NG_CALIBRATE` for a specific drive current) is persisted to your Klipper config. The data is stored in the probe’s main section by default, and you can optionally save multiple variants as named profiles.
- **Tap calibration** is rebuilt every time you run `PROBE_EDDY_NG_TAP`. No tap data is written to your config; it is always captured on-demand at the temperatures you are about to print with. This keeps tap data authoritative for your current conditions without stale values lingering on disk.

### Normal calibration profiles

You can snapshot a normal calibration and recall it later (for example: “cold bed”, “ABS chamber”, etc.).

1. Run `PROBE_EDDY_NG_SETUP` or `PROBE_EDDY_NG_CALIBRATE` to generate a good normal calibration.
2. Save it to a profile:
   ```
   PROBE_EDDY_NG_SAVE_SETUP PROFILE=<name>
   SAVE_CONFIG
   ```
3. Later, load it back (without re-running a calibration) with:
   ```
   PROBE_EDDY_NG_LOAD_SETUP PROFILE=<name>
   ```
   Omitting the `PROFILE` parameter (or using `PROFILE=default`) loads the default calibration stored in the probe section.

While any profile may hold multiple “normal” drive currents, tap drive currents are purposely excluded—tap calibrations will always be refreshed automatically during each tap command.

## Support

Questions? Come ask on the Sovol 3D Printers Discord at `https://discord.gg/Zg45rA52G7` in the eddy-ng forum. (Nothing Sovol-specific in `eddy-ng`, just where all this work started! You can also find the server via the Discover tab in Discord, then Sovol 3D Printers)

You can also file issues in [this `eddy-ng` github repo](https://github.com/vvuk/eddy-ng/issues).

## Installation

1. Clone this repository:

```
cd ~
git clone https://github.com/vvuk/eddy-ng
```

2. Run the install script:

```
cd ~/eddy-ng
./install.sh
```

(If your klipper isn't installed in `~/klipper`, provide the path as the first argument, i.e. `./install.sh ~/my-klipper`.)

3. Follow the rest of the full `eddy-ng` setup instructions that are [available in the wiki](https://github.com/vvuk/eddy-ng/wiki).

## Updating

Run a `git pull` and then run `./install.sh` again:

```
cd ~/eddy-ng
git pull
./install.sh
```

