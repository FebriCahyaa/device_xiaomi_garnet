# LineageOS device tree for Xiaomi garnet (6.6 kernel)

Base: `FebriCahyaa/device_xiaomi_garnet` branch `lineage-24.0`, commit
`5b3cb512c2f07f416eaa461e2ade28cead2e2760`. Local work happens on branch
`garnet-6.6`, matching the kernel, modules, DTS, vendor tree and XiaomiParts
forks.

## Workspace setup

This tree depends on repositories under a different GitHub account than
LineageOS. `lineage.dependencies` is only able to resolve `LineageOS/*`, so it
keeps just the upstream `hardware/xiaomi` entry. Clone this device tree first,
then copy the supplied local manifest **before** running `breakfast`:

```sh
mkdir -p device/xiaomi .repo/local_manifests
git clone -b garnet-6.6 https://github.com/FebriCahyaa/device_xiaomi_garnet.git device/xiaomi/garnet
cp device/xiaomi/garnet/manifests/garnet.xml .repo/local_manifests/garnet.xml
repo sync -c --force-sync
source build/envsetup.sh
breakfast lineage_garnet
```

The manifest intentionally does not list `device/xiaomi/garnet`, so `repo sync`
cannot replace the checkout that supplied the manifest.

The manifest pins these paths, which are the paths the existing `BoardConfig.mk`
already expects:

| Path | Repository |
|---|---|
| `vendor/xiaomi/garnet` | `FebriCahyaa/vendor_xiaomi_garnet` |
| `kernel/xiaomi/sm7435` | `FebriCahyaa/android_kernel_xiaomi_garnet_6.6` |
| `kernel/xiaomi/sm7435-devicetrees` | `FebriCahyaa/android_kernel_xiaomi_sm7435-devicetrees` |
| `kernel/xiaomi/sm7435-modules` | `FebriCahyaa/android_kernel_xiaomi_sm7435-modules` |
| `packages/apps/XiaomiParts` | `FebriCahyaa/packages_apps_XiaomiParts` |

## Proprietary files

`vendor/xiaomi/garnet` contains the generated makefiles plus the extracted
blobs (2024 files) from `OS3.0.5.0.WNRMIXM`. They are checked into that fork, so
no extraction is needed for a normal build. Re-extraction is only required when
switching to a different stock base:

```sh
device/xiaomi/garnet/extract-files.py /path/to/mounted/system
```

The extractor reads `proprietary-files.txt` and writes into the vendor tree.
See `vendor/xiaomi/garnet/PROVENANCE.md` for the mixed pinned-blob provenance.

## Kernel configuration

`BoardConfig.mk` builds the kernel with:

```
gki_defconfig
vendor/parrot_GKI.config
vendor/garnet_GKI.config
vendor/debugfs.config
```

`vendor/garnet_GKI.config` in our kernel fork carries the ZairenOX identity,
swap/zram writeback and schedutil settings used by this ROM. See
`PROVENANCE.md` in the kernel repository for the exact options.

External modules are built from `kernel/xiaomi/sm7435-modules`. The CVP sources
live in `qcom/opensource/eva-kernel-v1` on this branch, so there is no separate
`cvp-kernel` entry. `init_boot` and `system_dlkm` are included in `AB_OTA_PARTITIONS`
and have matching AVB/fstab wiring; VINTF still uses Android 12 FCM
(`target-level="6"`) and `BOARD_SHIPPING_API_LEVEL := 31`, unchanged from the
base tree.

XiaomiParts is platform-signed and installed as a privileged `system_ext` app.
Its 6.6-only controls read `/proc/sys/vm/*`, charging sysfs from
`qti_battery_charger_xiaomi`, cpufreq policy nodes and Adreno devfreq nodes.
`init.target.rc` and `sepolicy/vendor/xiaomi_parts.te` grant exactly those nodes;
legacy MiGT/Metis/gpu_plaid permission blocks were removed because no 6.6
driver or DTS node provides them.
