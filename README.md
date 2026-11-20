# ProjectBlaze 5 (Android 17 / LineageOS 24.0)

---

## Build for Devices (`default.xml`)

The default manifest syncs the multi-repository source tree so any device maintainer can build directly from `device/`, `vendor/`, and `kernel/` trees using standard `vendor/blaze` or `vendor/lineage` device makefiles.

```bash
mkdir -p ~/blaze && cd ~/blaze
repo init -u https://github.com/ProjectBlaze-Staging/blaze_manifest.git -b 17.0 --git-lfs
repo sync -c --no-clone-bundle --no-tags --optimized-fetch --prune --force-sync -j$(nproc --all)

source build/envsetup.sh
export LC_ALL=C

# Sync device/vendor/kernel trees (ProjectBlaze-Devices 17.0 -> LineageOS lineage-24.0 fallback)
breakfast <device>
# Or invoke explicitly with either prefix:
# lunch blaze_<device>-cp2a-userdebug
# lunch lineage_<device>-cp2a-userdebug

m blaze -j$(nproc --all)
```

> **Maintainer Flags**
> - `RELEASE_TYPE=OFFICIAL`: marks build `OFFICIAL` (`BLAZE_BUILD_TYPE`) and enables ProjectBlaze `Updater` pointing to `ProjectBlaze-Devices/official_devices` (`17.0/API/updater/{device}.json`). Default builds are `UNOFFICIAL`.
> - `WITH_GMS=true`: enables `GoogleWeather` alongside your authorized `vendor/gapps` / `vendor/partner_gms` tree (Blaze does not redistribute proprietary Google APKs).

### LiteGApps / Partner GMS variant

```bash
: "${PARTNER_GMS_URL:?Set PARTNER_GMS_URL to the authorized partner repository}"
git clone --depth=1 -b "${PARTNER_GMS_BRANCH:-17.0}" \
    "$PARTNER_GMS_URL" vendor/partner_gms
test -f vendor/partner_gms/products/gms_minimal.mk

source build/envsetup.sh
export LC_ALL=C
export BLAZE_BUILD_VARIANT=litegapps
export GMS_MAKEFILE=gms_minimal.mk
export WITH_GMS=true
lunch blaze_<device>-cp2a-userdebug
m blaze -j$(nproc --all)
```

---

## Build Universal Vanilla GSI (`gsi.xml`)

To build the public Vanilla Treble GSI (`ro.blaze.is_gsi=true`, `TrebleSettings` enabled, no bundled GApps or MicroG):

```bash
mkdir -p ~/blaze-gsi && cd ~/blaze-gsi
repo init -u https://github.com/ProjectBlaze-Staging/blaze_manifest.git -b 17.0 -m gsi.xml --git-lfs
repo sync -c --no-clone-bundle --no-tags --optimized-fetch --prune --force-sync -j$(nproc --all)

source build/envsetup.sh
export LC_ALL=C
lunch blaze_gsi_arm64-cp2a-userdebug
m systemimage -j$(nproc --all)
```

---

## Pixel 6a (`bluejay`) & Docs

- [Pixel 6a preparation and boot troubleshooting guide](docs/bluejay-preparation.md)
- [Pixel 6a recovery roadmap](docs/bluejay-recovery-roadmap.md)

---

## Hardware & Build Requirements

| Component | Minimum Requirement | Recommended |
| :--- | :--- | :--- |
| **OS** | Linux (BlazeOS Debian/Fedora series, Debian 12, Arch, Ubuntu) | BlazeOS Debian/Fedora series |
| **CPU** | 8 Cores / 16 Threads | 16+ Cores (AMD Ryzen / Intel Core i7/i9) |
| **RAM** | 16 GB (+ 16 GB Swap) (zRAM recommended) | 32 GB - 64 GB RAM |
| **Storage** | 400 GB Free Space | 500 GB+ NVMe SSD |
| **Java** | JDK selected by the Android build environment | Bundled prebuilt JDK |

---

## Credits & Acknowledgments

* [**Android Open Source Project (AOSP)**](https://android.googlesource.com)
* [**LineageOS**](https://github.com/LineageOS)
* [**PixelOS**](https://github.com/PixelOS-AOSP)
* [**Evolution X**](https://github.com/Evolution-X)
* [**ColorCC (Guest / addictedcaf)**](https://github.com/addictedcaf/ColorCC) — ColorOS-inspired Liquid Glass Control Centre & Spring Physics Engine
* [**Project Blaze Team**](https://github.com/ProjectBlaze-Staging)
