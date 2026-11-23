# LineageOS device tree for Xiaomi garnet (6.6 kernel)

Base: `FebriCahyaa/device_xiaomi_garnet` branch `lineage-24.0`, commit
`5b3cb512c2f07f416eaa461e2ade28cead2e2760`. Local work happens on branch
`garnet-6.6`, matching the kernel, modules, DTS, vendor tree and XiaomiParts
forks.

## Workspace setup

This tree depends on repositories under a different GitHub account than
LineageOS. `lineage.dependencies` is only able to resolve `LineageOS/*`, so it
keeps just the upstream `hardware/xiaomi` entry. Copy the supplied local
manifest **before** running `breakfast`:

```sh
mkdir -p .repo/local_manifests
cp device/xiaomi/garnet/manifests/garnet.xml .repo/local_manifests/garnet.xml
repo sync -c --force-sync
source build/envsetup.sh
breakfast lineage_garnet
```

The manifest pins these paths, which are the paths the existing `BoardConfig.mk`
already expects:

| Path | Repository |
|---|---|
| `device/xiaomi/garnet` | `FebriCahyaa/device_xiaomi_garnet` |
| `vendor/xiaomi/garnet` | `FebriCahyaa/vendor_xiaomi_garnet` |
| `kernel/xiaomi/sm7435` | `FebriCahyaa/android_kernel_xiaomi_garnet_6.6` |
| `kernel/xiaomi/sm7435-devicetrees` | `FebriCahyaa/android_kernel_xiaomi_sm7435-devicetrees` |
| `kernel/xiaomi/sm7435-modules` | `FebriCahyaa/android_kernel_xiaomi_sm7435-modules` |
| `packages/apps/XiaomiParts` | `FebriCahyaa/packages_apps_XiaomiParts` |

If you cloned the device tree manually instead of syncing the manifest, remove
the first `<project>` entry from your local copy so `repo` does not try to
replace it.

## Proprietary files

`vendor/xiaomi/garnet` contains makefiles and the extractor configuration, not
the blobs themselves. From a fully synced workspace:

```sh
device/xiaomi/garnet/extract-files.py
```

The script reads `proprietary-files.txt` from a stock ROM mounted at
`/mnt/garnet` (or the path given on the command line). Extracted binaries are
not committed to git.

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
