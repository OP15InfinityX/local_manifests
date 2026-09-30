# OnePlus 15 Infinity-X 4.0

Local manifests for Infinity-X 4.0 / Android 17 on the OnePlus 15 (`infiniti`).

This branch keeps the tested Infinity-X 3.12 device/common baseline, adds the
required Android 17 adaptations, and retains OxygenOS **16.0.8 GLO** proprietary
files. Audio and display HALs remain stock. The kernel is built from source:
Linux **6.12.81**, including the existing panel and module fixes.

The `4.0` branches include the 2026-09-29 Infinity-X final feature-restoration
merges in frameworks/base, Settings and InfinitySuite, preserving the OP15
adaptations and duress PIN support. Custom-clock/AOD handling and the required
HideClock, HideSmartSpace and SmartSpaceOffset overlays are included; the overlay
packages are supplied by the upstream `packages/overlays/Themes` branch `17`.
The ROM version is `4.0`, not `4.0-BETA`.

The 2026-09-30 kernel update adds 27 selected fixes across the kernel, modules
and devicetrees repositories. Original authors and upstream commit IDs are
retained in the individual commits. The common kernel remains Linux 6.12.81.

The tested SetupWizard locale configuration is maintained in
`vendor/infinity/config/gms.mk`; Google binaries remain in the upstream GMS
repository. The device disables locale-agnostic onboarding to retain language
selection, and Settings includes the language-picker crash fix.

Vanilla builds include the Infinity-X partition reserve configuration: about
1.82 GiB in `product` and 90 MiB each in `system` and `system_ext` for later
GApps installation. These extra reserves are not applied to GApps builds.

## Sync

Run in an empty source directory:

```bash
repo init --no-repo-verify --git-lfs \
  -u https://github.com/ProjectInfinity-X/manifest \
  -b 17 -g default,-mips,-darwin,-notdefault

git clone -b 4.0 https://github.com/OP15InfinityX/local_manifests \
  .repo/local_manifests

repo sync -c --no-clone-bundle --no-tags --optimized-fetch --prune \
  --force-sync -j$(nproc --all)
```

Do not combine these XML files with the 3.12 local manifests: they override many
of the same project paths. Back up personal changes before syncing an existing
tree. The OP15-specific forks referenced here use the `4.0` branch.

The manifests follow the same three-file layout as the 3.12 branch:

- `infiniti.xml`: device, common, camera, hardware and CAF dependencies.
- `infiniti-kernel.xml`: source-built kernel, modules and devicetrees.
- `op15-4.0.xml`: ROM/platform repository overrides.

All three XML files are loaded automatically by repo.

## Extract proprietary files

Download the full OnePlus 15 OxygenOS **16.0.8 GLO** OTA. Extract the logical
images into partition directories (`system`, `system_ext`, `product`, `vendor`,
`odm`, and the remaining stock partitions).

From the ROM source root, replace the example path below with your extracted
firmware directory. Run extraction before lunch on a fresh checkout:

```bash
export PYTHONPATH="$PWD/tools/extract-utils"
export STOCK_ROOT="/path/to/extracted/OP15"

python3 device/oneplus/infiniti/extract-files.py --only-target "$STOCK_ROOT"
python3 device/oneplus/sm8850-common/extract-files.py --only-target "$STOCK_ROOT"
python3 device/oneplus/infiniti-camera/extract-files.py --only-target "$STOCK_ROOT"
```

All three commands must finish successfully. The extraction scripts include the
required Android 17 blob fixups; copying unmodified stock blobs is not equivalent.
Proprietary files are not uploaded to this manifest repository.

## Build

```bash
export WITH_GAPPS=true
export USE_PREBUILT_KERNEL=false
source build/envsetup.sh
lunch infinity_infiniti-user
m bacon -j$(nproc --all)
```

Use `WITH_GAPPS=false` for Vanilla. Adjust the job count to suit your host.
Artifacts are written to `out/target/product/infiniti/`.

## Scope

- Existing 3.12 and Lineage branches are unchanged.
- Personal Java/dex2oat workarounds, host-specific build profiles, and diagnostic
  ADB authorization keys are not included.
- Unfinished additional eSIM switching changes and Widevine/L1 experiments are
  intentionally withheld from this publication.
- The final ROM has booted on the maintainer's device. The maintainer also
  reports the selected kernel fixes running without errors. A fresh checkout
  and full rebuild of the complete published state have not been performed.
