# OnePlus 15 Infinity-X 4.0

Local manifests for Infinity-X 4.0 / Android 17 on the OnePlus 15 (`infiniti`).

This branch keeps the tested Infinity-X 3.12 device/common baseline, adds the
required Android 17 adaptations, and retains OxygenOS **16.0.8 GLO** proprietary
files. Audio and display HALs remain stock. The kernel is built from source:
Linux **6.12.81**, including the existing panel and module fixes.

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
- The additional eSIM switching fix and the unfinished SetupWizard language
  selection changes are intentionally withheld from this publication.
- The local device build completed successfully before publication. These
  published branches have not yet been validated with a fresh checkout and full
  rebuild; device behavior still needs normal testing.
