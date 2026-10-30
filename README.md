# OnePlus 15 Infinity-X 4.1

Local manifests for Infinity-X 4.1 / Android 17 on the OnePlus 15 (`infiniti`).
This branch uses `4.1-cos17` for every OP15-owned project. It is also the
default branch of the OP15InfinityX repositories used here. Existing `4.0`
branches remain available.

This branch keeps the tested Infinity-X 3.12 device/common baseline, adds the
required Android 17 adaptations, and uses ColorOS **17.0.0.102 IN** proprietary
files. Audio services/plugins, display and IPACM use the tested LineageOS 24
source stack with the required COS17 proprietary extensions. The kernel is built from source:
Linux **6.12.81**, including the existing panel and module fixes.

The OP15 forks include the 2026-09-29 Infinity-X final feature-restoration
merges in frameworks/base, Settings and InfinitySuite, preserving the OP15
adaptations and duress PIN support. Custom-clock/AOD handling and the required
HideClock, HideSmartSpace and SmartSpaceOffset overlays are included; the overlay
packages are supplied by the OP15 `packages_overlays_Themes` branch `4.1-cos17`.
The ROM version is now `4.1`.

The 2026-09-30 kernel update adds 27 selected fixes across the kernel, modules
and devicetrees repositories. Original authors and upstream commit IDs are
retained in the individual commits. The common kernel remains Linux 6.12.81.

The 2026-10-02 update incorporates the reviewed official Infinity-X platform
updates into 17 repositories, retaining the OP15 customizations and GameSpace
callback integration. Six additional platform forks are selected by the
manifests so a sync retrieves the reviewed state. Five selected Chandu kernel
fix groups cover microphone-regulator failures, display fence safety, WLAN
region/BDF fallback, touch-array bounds, and charger interface offsets. These
are targeted adaptations, not wholesale imports of other devices' sources.
The maintainer reports this combined update working on the device.

The 2026-10-13 update incorporates the tested official Infinity-X 4.1 changes,
including the October 2026 security patch, official PIF boot-deadlock fix,
launcher and Infinity Suite improvements, Bluetooth updates, and the matching
sandbox and SELinux changes. OP15 features, duress PIN, stock camera/gallery
integration, separate Vanilla/GApps OTA channels, recovery/ABL preservation,
and zstd OTA compression are retained. The new AOD brightness control is
integrated with the existing adaptive low/high brightness and pickup boost.
Repositories that exactly match the reviewed official 4.1 sources are pinned
to those commits; OP15 customizations remain in the `4.1-cos17` forks.

The 2026-10-29 source update incorporates the latest published Infinity-X
Glass blur renderer and SystemUI updates, launcher and Settings fixes,
the optional custom AOD brightness switch, and the matching Sandbox fix.
The official manifest dependency changes are included. OP15-specific
AOD low/high brightness and pickup boost, Dynamic Island controls,
fingerprint/PIN behavior, camera compatibility, SetupWizard locale handling,
OTA variants and zstd remain in place. Build and device testing are pending.

The 2026-10-30 integration merges the matching Infinity-X updates in
frameworks/base, InfinitySuite, Settings, custom-sdk, Parts and telephony.
It includes the Play Integrity/TrickyStore changes, LiveDisplay updates,
multi-SIM ringtone handling and WAP-Push header validation. Telecomm and
the Android 17 OmniStyle are pinned to the exact upstream commits used by
the successful GApps and Vanilla full builds. OP15 PlusKey, Dynamic Island,
AOD, duress PIN, camera compatibility and OTA customizations are retained.
Device testing of this latest integration remains pending.
The DT2W setting also remains effective across reboots: restoring other
off-screen gestures preserves its state, and PowerManager applies the
saved setting at startup. This DT2W fix was verified on the device.

The additional 2026-10-29 fixes correct Codec2 interface declarations,
allow the sensor-health monitor to read clocks, derive Power HAL capability
masks from its supported modes/boosts, and avoid unwanted wake-ups when the
power manager stops AOD. Original authors and Change-Ids are retained.
The Power HAL is selected from the OP15 `android_vendor_qcom_opensource_power`
fork on `4.1-cos17`; unchanged IX manifest dependencies are pinned to the
reviewed commits so new syncs use the same interfaces and certificate set.

The touch modules also include Akshay Kakatkar's S3910 single-/double-tap
coexistence fix (LineageOS Gerrit change 507346, patchset 3), tested positively
by Chandu. With both gestures enabled, a single tap is delayed by up to 300 ms
to distinguish a double tap. The pending ADFR/QSync LTPO changes 504300/504301
are not included. The common kernel remains Linux 6.12.81.

The existing SetupWizard locale configuration is maintained in
`vendor/infinity/config/gms.mk`; Google binaries remain in the upstream GMS
repository. The device disables locale-agnostic onboarding to retain language
selection, and Settings includes the language-picker crash fix.

Vanilla builds include the Infinity-X partition reserve configuration: about
1.82 GiB in `product` and 90 MiB each in `system` and `system_ext` for later
GApps installation. These extra reserves are not applied to GApps builds.

## COS17 port (2026-10-23)

All three proprietary-file lists now select the OnePlus 15 CPH2745
**17.0.0.102(EX01), IN** full OTA. Extract the required stock partitions,
including `my_product`, `my_stock`, `my_bigball`, `my_heytap`, `my_carrier` and
`my_region` when present, along with the standard Android partitions and the
firmware images. Keep the partition directory names from the OTA; do not mix
these inputs with files extracted from the old GLO OTA.

The port includes:

- COS17 firmware/native/app inputs and matching vendor interface versions.
- The missing IMS `libSurfaceUtil_jni` app-library dependency.
- The correct Surface JNI return signature used by the new camera libraries.
- Material parameter APIs and checks for the actual proprietary renderer APIs.
- The original AppFeature provider classes and database contract.
- Mind Space's consent permission and both AIUnit search preference XML roots.
- The Recorder Activity state wrapper and Oplus policy-file return contract.
- Chandu's HBP5 report-interface patch, with original author and Change-Id,
  and the matching enforcing device-node label.

FBE remains enabled in the published source. The OSS kernel and all previously
published OP15 customizations are retained. The later source-HAL migration is
described below. Newly identified, unvalidated logspam fixes, host build workarounds,
private ADB keys and unrelated local platform experiments are excluded.

The maintainer has built and booted this COS17 device/app port with SELinux
Enforcing and tested the main apps. No claim is made that every camera mode or
all remaining OEM diagnostics have been resolved. A clean build from this exact
new manifest has not been performed by the publishing agent.

## Source HAL migration (2026-10-27)

The device now uses the matching LineageOS 24 COS17 source components:

- Audio services, AGM/AudioReach, SoundTrigger, and the compatible PAL plugins.
  The proprietary audio core/effect HALs, PAL core and required OEM session,
  Bluetooth and speaker libraries are retained. This is a supported hybrid
  configuration, rather than a claim that every audio component is open source.
  Device routes, Quasar and ViPER remain enabled. The source daemon uses the
  stock DSP library path, and ACDB is installed at the path expected by AGM.
- Display composer, allocator, mapper, demura service and matching display core.
  OEM GPU/color/Pixelworks extensions remain proprietary. The source stack
  forwards the existing OnePlus auto-ADFR request to the kernel's
  `qsync_min_fps` property independently of standard QSync. AOD uses active
  low-power doze so Pulse and charging animations keep receiving frames; LTPO
  handles the idle minimum refresh rate independently.
- IPACM, its configurations, IPANAT and the AIDL tethering offload implementation.
  The obsolete flag suppressing the source IPACM namespace is removed.
- FusionLight wraps the retained Qualcomm sensors SubHAL with the source ALS
  correction algorithm. The device selects the matching stock sensor properties,
  BOE/Tianma TCS3760 profiles, and the OPlus sensor calibration service. Both
  legacy and black-frame V2.1 profiles are supported by the source wrapper.
- The charger reports an unavailable charge-time estimate as missing data,
  allowing Android to use its charging history instead of interpreting zero as
  an immediate full charge. Valid estimates and zero at full charge are retained.
- The COS17 offline camera metadata fix reads the allocation size from the
  source SnapHandle layout. It is applied during extraction; no manually
  modified blob or second OTA is needed.

The radio compatibility interface, threaded Vulkan RenderEngine and vendor
PAL member-layout fixes are included with their original upstream history.
The unmodified HAL dependencies are pinned to the tested commits; the display
core is an OP15 fork because it carries the auto-ADFR bridge. The existing
wallpaper clock-preview adaptation and the koaaN/Daniel boot animation are
also included so a fresh sync matches the distributed builds.

The maintainer tested boot, camera capture, hotspot, microphone, telephony,
media audio and LTPO/AOD on the resulting build. The source IPACM binary and
libraries were checked against the installed build, and its AIDL offload
service starts with SELinux Enforcing. The agent has not run a separate clean
full build. Proprietary files are generated by the extraction commands below;
they are not committed to these source repositories.

## Sync

Run in an empty source directory:

```bash
repo init --no-repo-verify --git-lfs \
  -u https://github.com/ProjectInfinity-X/manifest \
  -b 17 -g default,-mips,-darwin,-notdefault

git clone -b 4.1-cos17 https://github.com/OP15InfinityX/local_manifests \
  .repo/local_manifests

repo sync -c --no-clone-bundle --no-tags --optimized-fetch --prune \
  --force-sync -j$(nproc --all)
```

Do not combine these XML files with the 3.12 local manifests: they override many
of the same project paths. Back up personal changes before syncing an existing
tree. The OP15-specific forks referenced here use the `4.1-cos17` branch.

The manifests follow the same three-file layout as the 3.12 branch:

- `infiniti.xml`: device, common, camera, hardware and CAF dependencies.
- `infiniti-kernel.xml`: source-built kernel, modules and devicetrees.
- `op15-4.1.xml`: ROM/platform repository overrides.

All three XML files are loaded automatically by repo.

## Extract proprietary files

Download the full OnePlus 15 ColorOS **17.0.0.102 IN** OTA. Extract the logical
images into partition directories (`system`, `system_ext`, `product`, `vendor`,
`odm`, and the remaining stock partitions).

From the ROM source root, replace the example path below with your extracted
firmware directory. Run extraction before lunch on a fresh checkout:

```bash
export PYTHONPATH="$PWD/tools/extract-utils:$PWD/hardware/oplus/extract-utils-ext"
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
