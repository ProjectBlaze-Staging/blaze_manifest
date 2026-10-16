# Pixel 6a (bluejay) preparation and boot troubleshooting

This document distinguishes a normal full-ROM installation from DSU. They do
not replace the same partitions and must not share flashing instructions.

## Before building

1. Sync the complete tree using the command for the selected build variant in
   the main README.
2. Confirm that `vendor/lineage/prebuilt/common/app/Firefox/Firefox.apk` is a
   real APK, not a small Git LFS pointer file.
3. Use only `lineage_bluejay-cp2a-userdebug`. Bluejay is Pixel 6a; images for
   oriole, raven, panther, or any other Pixel are not interchangeable.
4. Keep every artifact from one completed build together. Record the manifest
   revision and SHA-256 sums before publishing.
5. Run at least `m bootimage vendorbootimage dtboimage bacon`. Do not publish a
   ZIP assembled from one build alongside boot images from another build.

The current device configuration identifies its vendor/fingerprint base as
Google build `CP2A.260705.006` with a 2026-07-05 vendor/boot security patch.
The checked-in kernel module bundle is labelled `CP2A.260605.012`. This is the
current upstream Lineage device-tree combination, but it must be tested as a
set; replacing only one side with EvolutionX or Infinity-X files is unsafe.

## Full-ROM installation

Back up all data and keep Google's current bluejay factory image available for
recovery. The bootloader must be unlocked. Never relock it with an unofficial
build installed.

Bluejay has no standalone recovery partition. Recovery resources are carried
by the boot/vendor-boot chain. For a first installation, use these three files
from the exact same Blaze build:

```bash
adb reboot bootloader
fastboot getvar product
fastboot getvar current-slot
fastboot flash boot boot.img
fastboot flash dtbo dtbo.img
fastboot flash vendor_boot vendor_boot.img
fastboot reboot recovery
```

`fastboot getvar product` must report `bluejay`. Stop on any fastboot error.
From Blaze recovery, format data when crossing from another ROM or encryption
configuration, then sideload the matching Blaze OTA ZIP. Formatting data is
destructive; make the backup before entering recovery.

Do not reuse EvolutionX/Infinity-X recovery images merely because they boot.
Their kernel, DTBO, vendor ramdisk modules, fstab, AVB metadata, and SELinux
policy may describe a different monthly base.

## DSU installation

DSU temporarily replaces logical system-side partitions; it does not turn a
full device OTA ZIP into a DSU package and it does not replace the host
bootloader, kernel, vendor_boot, modem, or all vendor firmware.

Before enabling DSU:

```bash
adb shell getprop ro.product.device
adb shell getprop ro.product.cpu.abi
adb shell getprop ro.vndk.version
adb shell getprop ro.build.version.release
adb shell getprop ro.boot.slot_suffix
```

The package must target `bluejay`/`arm64`, must not be older than the host OS,
and must be compatible with the host VNDK/vendor interface. Do not flash the
full-ROM boot/dtbo/vendor_boot images merely to test a DSU. Start with the
vanilla DSU so GMS setup cannot hide a lower-level boot failure.

## Why a device can bootloop after another ROM

The strongest architecture-backed cause is a mixed boot stack:

- Bluejay recovery lives in boot/vendor_boot instead of a separate recovery
  partition.
- `vendor_boot` contains first-stage ramdisk files and kernel modules.
- `dtbo` must match the kernel and hardware description.
- logical system/vendor partitions are AVB-chained through vbmeta.
- the ROM carries monthly radio/boot firmware images, while DSU continues to
  use most of the host firmware and vendor stack.

Flashing only the ROM ZIP, only `boot.img`, or an image retained from
EvolutionX/Infinity-X can therefore fail before Android can produce a normal
userspace log. A dirty `/data`, mismatched encryption metadata, wrong active
slot, rollback protection, or an incompatible DSU host vendor can produce a
similar symptom.

Without the failing device's logs this is a ranked root-cause analysis, not a
claim that every reported bootloop had one identical cause.

## Evidence to collect before attempting fixes

Record every command and do not randomly flash individual partitions:

```bash
fastboot getvar product
fastboot getvar current-slot
fastboot getvar slot-count
fastboot getvar unlocked
fastboot getvar secure
fastboot getvar all 2>&1 | tee fastboot-vars.txt
```

If recovery boots, collect:

```bash
adb devices
adb pull /tmp/recovery.log
adb shell cat /proc/cmdline > recovery-cmdline.txt
```

If Android starts far enough for ADB:

```bash
adb logcat -b all -d > logcat-all.txt
adb shell dmesg > dmesg.txt
adb shell getprop > getprop.txt
```

For DSU failures also capture `adb shell gsi_tool status` and the Dynamic
System Updates notification/error before rebooting back to the host system.

## Recovery path

If the matching Blaze boot stack still fails, return to the official Google
bluejay full OTA/factory image, boot it once, and make sure both A/B slots are
healthy before retrying. Do not copy random firmware partitions from another
custom ROM. Factory-image flashing can erase data and must follow Google's
official instructions for the exact device.

## References

- [Google Pixel factory images](https://developers.google.com/android/images)
- [AOSP Dynamic System Updates documentation](https://source.android.com/docs/core/ota/dynamic-system-updates)
- [EvolutionX bluejay installation page](https://evolution-x.nl/device/bluejay)
- [LineageOS Google Apps guidance](https://wiki.lineageos.org/gapps/)
