# Blaze Recovery roadmap for bluejay

Bluejay is an A/B device without a standalone recovery partition. Recovery is
assembled into the boot/vendor_boot chain, so recovery development must ship
and test matching `boot.img`, `dtbo.img`, and `vendor_boot.img` artifacts.

## Phase 0: reproducible baseline

- Build `bootimage`, `vendorbootimage`, `dtboimage`, and the OTA from one
  manifest revision.
- Publish SHA-256 sums and the exact stock firmware/fingerprint base.
- Verify touch, display, ADB, sideload, factory reset, slot selection,
  fastbootd, reboot targets, and recovery logs on bluejay.
- Test entry from both A and B slots and from stock as well as Blaze.

## Phase 1: installation safety

- Add a visible Blaze/bluejay build identity and build date.
- Display active slot, battery level, and verified device codename.
- Refuse installation packages for another device or older incompatible
  firmware metadata.
- Improve error messages for signature, dynamic-partition, and low-space
  failures; always preserve `/tmp/recovery.log` for ADB retrieval.
- Document matching boot/dtbo/vendor_boot requirements in the release assets.

## Phase 2: recovery reliability

- Audit `fstab.gs101`, first-stage mounts, metadata, and F2FS tools against the
  Android 17 device tree.
- Validate every module in `vendor_boot.modules.load`; confirm the bluejay
  touch module loads without manual intervention.
- Test sideload interruption, low battery, USB reconnect, failed OTA,
  snapshot merge, and fastbootd transitions.
- Add automated host-side smoke tests for recovery image contents, AVB
  descriptors, partition sizes, and required ramdisk files.

## Phase 3: usability without weakening security

- Improve accessible navigation, translations, status reporting, and log
  export.
- Add clear recovery/fastbootd distinction and destructive-action warnings.
- Keep package signature verification and AVB enabled. Do not add blanket
  verification bypasses or automatic data decryption shortcuts.

## Release gate

A recovery build is publishable only when its matching ROM boots after a clean
install, an OTA succeeds on both slots, rollback to the previous known-good
slot works, and all published image hashes correspond to the same manifest
revision. DSU testing is a separate gate and must not be presented as proof
that full-ROM recovery installation works.
