# Provenance - device_xiaomi_garnet

## Base

- Repository: `https://github.com/FebriCahyaa/device_xiaomi_garnet`
- Upstream base branch: `lineage-24.0`
- Base commit kept intact: `5b3cb512c2f07f416eaa461e2ade28cead2e2760`
  (`garnet: Wire up per fps sf durations`, AdarshGrewal)
- All original copyright headers, authors and `Signed-off-by` trailers in the
  inherited tree are preserved.
- Local branch: `garnet-6.6`. `origin` points at this fork.

## Local integration commits

Authored as `FEBRIAN RAHMAD CAHYA <87246800+FebriCahyaa@users.noreply.github.com>`
and committed with `git commit -s`:

- `manifests/garnet.xml`: local manifest pinning the six FebriCahyaa forks to
  `garnet-6.6`, including the kernel paths `BoardConfig.mk` already expects.
- `lineage.dependencies`: removed the three kernel entries that LineageOS
  roomservice would resolve to `LineageOS/*`; kept `android_hardware_xiaomi`.
  Roomservice cannot point at another GitHub account, hence the local manifest.
- `device.mk`: added `XiaomiParts` to `PRODUCT_PACKAGES` and removed a
  duplicated stepdetector permission copy rule. `NcmTetheringOverlay` is kept;
  it is provided by `vendor/lineage/overlay/rro_packages/NcmTetheringOverlay`.
- `sepolicy/vendor/{file.te,genfs_contexts,xiaomi_parts.te}`: narrow labels and
  rules for the 6.6 charging, thermal, cpufreq, Adreno devfreq, DRM and
  `/proc/sys/vm` nodes XiaomiParts uses. AOSP `system_app` has no sysfs/procfs
  write rule by default; custom types avoid broadening the generic `sysfs` and
  `proc` labels.
- `rootdir/etc/init.target.rc`: corrected the LED path typo and `chown system`
  calls, set `input_suspend` to the ueventd mode `0660` and made
  `thermal_message/sconfig` writable by XiaomiParts, switched memory tunables
  to the `/proc/sys/vm` paths actually used by UMM, added CPU `online` nodes,
  and removed the dead MiGT/Metis/gpu_plaid block (no matching 6.6 driver or
  DTS node).
- `rootdir/etc/fstab.qcom` and `BoardConfig.mk`: wired `init_boot` and
  `system_dlkm` for the GKI 6.6 build (partition size, AVB chain, filesystem
  type, copy-out, blocklist and fstab mounts), and pointed the external module
  roots at the paths that exist in `sm7435-modules`.
- `modules/modules.list.second_stage`: dropped modules already loaded in the
  first stage and `qcom-cpufreq-hw-debug.ko`, which has no enabled Kconfig.
- `configs/hidl/manifest.xml`: `target-level` bumped to `7` and
  `BOARD_SHIPPING_API_LEVEL`/`PRODUCT_SHIPPING_API_LEVEL` bumped to `33` to match
  garnet's launch API/FCM and LineageOS 24.0 expectations. The manifest content
  matches the lineage-24.0 garnet manifest; still revalidate VINTF in a full
  tree. `vendor.qti.hardware.qccsyshal@1.2` remains in `framework_manifest.xml`
  only and is not duplicated in the device manifest.
- `overlay/*`: unchanged. The GL eSIM overlay deliberately requires both
  `ro.boot.hardware.sku=garnet` and `ro.boot.product.hardware.sku=GL`, as in
  the upstream fix that distinguishes global garnet from garnetp.
- `extract-files.py`: removed the stale `pureShot_parameter.xml` fixup; that
  file is not in `proprietary-files.txt`.
- `README.md`: setup, checked-in blobs and 6.6 integration notes.

`proprietary-files.txt`, `Android.bp` and the generated vendor makefiles remain
as inherited from the audited base commit. The existing `REQUIRED=` entries
point at modules supplied by `vendor_qcom_opensource_core-utils-vendor` and
`hardware_qcom-caf_common`, so no blob dependency metadata was removed.

## Companion repositories

| Repository | Branch |
|---|---|
| `FebriCahyaa/vendor_xiaomi_garnet` | `garnet-6.6` |
| `FebriCahyaa/android_kernel_xiaomi_garnet_6.6` | `garnet-6.6` |
| `FebriCahyaa/android_kernel_xiaomi_sm7435-devicetrees` | `garnet-6.6` |
| `FebriCahyaa/android_kernel_xiaomi_sm7435-modules` | `garnet-6.6` |
| `FebriCahyaa/packages_apps_XiaomiParts` | `garnet-6.6` |

## Known limits

No LineageOS/AOSP checkout, Java/Kotlin toolchain or cross compiler is present
in this environment. Verification here is static only: JSON/XML well-formedness
and `git diff --check`. A full `breakfast lineage_garnet` must be run on a
normal build machine.
