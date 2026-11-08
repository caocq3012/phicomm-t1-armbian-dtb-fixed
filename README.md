# meson-gxm-phicomm-t1-fixed.dtb

[English](https://github.com/caocq3012/phicomm-t1-armbian-dtb-fixed/blob/main/README_EN.md)

斐讯 T1（Amlogic S912 / GXM）Armbian 专用设备树文件，修复 eMMC 无法识别的问题。

## 问题

斐讯 T1 使用 Armbian 镜像自带的 `meson-gxm-q201.dtb` 或 `meson-gxm-phicomm-t1.dtb` 启动时，eMMC 在初始化阶段 tuning 失败：

    mmc1: tuning execution failed: -5
    mmc1: error -5 whilst initialising MMC card

表现为 `lsblk` 中看不到 `mmcblk` 设备，`armbian-install` 无法将系统写入内部存储。

## 本文件的修改内容

基于 `meson-gxm-q201.dtb` 反编译修改，仅改动 eMMC 节点 `mmc@74000`：

- `max-frequency`：`0x5f5e100`（100MHz）→ `0x17d7840`（25MHz）
- 移除 `mmc-ddr-1_8v`
- 移除 `mmc-hs200-1_8v`

其余节点（WiFi/SDIO `mmc@70000`、SD 卡 `mmc@72000` 等）保持原样。

## 使用方法

1. 将本文件复制到 Armbian 启动盘 BOOT 分区的 `dtb/amlogic/` 目录
2. 编辑 BOOT 分区根目录的 `uEnv.txt`，将 `FDT=` 指向本文件：

    FDT=/dtb/amlogic/meson-gxm-phicomm-t1-fixed.dtb

3. 保存后重启，验证 eMMC 是否识别：

    lsblk

应能看到 `mmcblk1` 及 `mmcblk1boot0` / `mmcblk1boot1`：

    mmcblk1      179:0    0 14.6G  0 disk
    mmcblk1boot0 179:32   0    4M  1 disk
    mmcblk1boot1 179:64   0    4M  1 disk

## 适用环境

- 设备：斐讯 T1（Amlogic S912 / GXM）
- 系统：Armbian（基于 `meson-gxm-q201.dtb` 的镜像）
- 内核：5.x 系列

## 备注

如果使用本文件后 eMMC 仍无法识别，可将 `max-frequency` 进一步降至 12.5MHz（`0xBEBC20`）后重新编译。
同时附赠img镜像可以自行烧录并修改dtb
