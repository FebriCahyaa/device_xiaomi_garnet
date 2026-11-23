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
- `device.mk`: added `XiaomiParts` to `PRODUCT_PACKAGES` so the platform-signed
  system_ext app from our fork is installed.
- `README.md`: setup and blob-extraction instructions.

No vendor blob, overlay value, partition size, AVB key or sepolicy rule from
the base tree was changed.

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
