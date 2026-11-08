# meson-gxm-phicomm-t1-fixed.dtb

Device tree file for Phicomm T1 (Amlogic S912 / GXM) on Armbian, fixing eMMC detection issues.

## Problem

When booting Armbian with the stock `meson-gxm-q201.dtb` or `meson-gxm-phicomm-t1.dtb` on a Phicomm T1, eMMC initialization fails during the tuning phase:

    mmc1: tuning execution failed: -5
    mmc1: error -5 whilst initialising MMC card

As a result, no `mmcblk` device appears in `lsblk`, and `armbian-install` cannot write the system to internal storage.

## What this file changes

Decompiled from `meson-gxm-q201.dtb` and modified only in the eMMC node `mmc@74000`:

- `max-frequency`: `0x5f5e100` (100MHz) → `0x17d7840` (25MHz)
- Removed `mmc-ddr-1_8v`
- Removed `mmc-hs200-1_8v`

All other nodes (WiFi/SDIO `mmc@70000`, SD card `mmc@72000`, etc.) are left unchanged.

## Usage

1. Copy this file to the `dtb/amlogic/` directory on the BOOT partition of your Armbian boot media
2. Edit `uEnv.txt` in the root of the BOOT partition and point `FDT=` to this file:

    FDT=/dtb/amlogic/meson-gxm-phicomm-t1-fixed.dtb

3. Save, reboot, and verify that eMMC is detected:

    lsblk

You should see `mmcblk1` along with `mmcblk1boot0` / `mmcblk1boot1`:

    mmcblk1      179:0    0 14.6G  0 disk
    mmcblk1boot0 179:32   0    4M  1 disk
    mmcblk1boot1 179:64   0    4M  1 disk

## Compatibility

- Device: Phicomm T1 (Amlogic S912 / GXM)
- System: Armbian (images based on `meson-gxm-q201.dtb`)
- Kernel: 5.x series

## Notes

If eMMC is still not detected with this file, lower `max-frequency` further to 12.5MHz (`0xBEBC20`) and recompile.

An IMG image is also included, which you can flash yourself and modify the DTB as needed.
