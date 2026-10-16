# BlazeAOSP (Android 17 / ProjectBlaze 5)

---

## Build BlazeAOSP

### Vanilla: sync and build

This downloads only the public Blaze/AOSP/Lineage sources and produces a ROM
without Google apps.

```bash
mkdir -p ~/blaze && cd ~/blaze
repo init -u https://github.com/ProjectBlaze-Staging/blaze_manifest.git -b 17.0
repo sync -c --no-clone-bundle --no-tags --optimized-fetch --prune --force-sync -j$(nproc --all)

# Firefox is stored with Git LFS in android_vendor_blaze.
git lfs install
git -C vendor/lineage lfs pull

source build/envsetup.sh
export LC_ALL=C
export BLAZE_BUILD_VARIANT=vanilla
lunch lineage_bluejay-cp2a-userdebug
m bacon -j$(nproc --all)
```

### LiteGApps-integrated: sync and build

The public source sync is the same, but this variant additionally requires an
authorized Android 17-compatible partner tree. The public LiteGApps installer
repository is not a `vendor/partner_gms` source tree. Blaze does not download
or redistribute proprietary Google APKs.

```bash
mkdir -p ~/blaze && cd ~/blaze
repo init -u https://github.com/ProjectBlaze-Staging/blaze_manifest.git -b 17.0
repo sync -c --no-clone-bundle --no-tags --optimized-fetch --prune --force-sync -j$(nproc --all)

git lfs install
git -C vendor/lineage lfs pull

# Set this to your team's authorized partner source and branch.
: "${PARTNER_GMS_URL:?Set PARTNER_GMS_URL to the authorized partner repository}"
git clone --depth=1 -b "${PARTNER_GMS_BRANCH:-17.0}" \
    "$PARTNER_GMS_URL" vendor/partner_gms
test -f vendor/partner_gms/products/gms_minimal.mk

source build/envsetup.sh
export LC_ALL=C
export BLAZE_BUILD_VARIANT=litegapps
export GMS_MAKEFILE=gms_minimal.mk
lunch lineage_bluejay-cp2a-userdebug
m bacon -j$(nproc --all)
```

The two variants use the same reviewed source revision. The build fails early
if the LiteGApps partner tree is missing, which prevents a mislabeled vanilla
image from being released as LiteGApps.

## Pixel 6a installation and DSU

Read [the bluejay preparation and boot troubleshooting guide](docs/bluejay-preparation.md)
before installing. Do not mix `boot.img`, `dtbo.img`, or `vendor_boot.img`
from EvolutionX, Infinity-X, stock, or another Blaze build.

The recovery improvement work is tracked in the
[bluejay recovery roadmap](docs/bluejay-recovery-roadmap.md).

---

## Hardware & Build Requirements

To build BlazeAOSP from source, your workstation should meet the following minimum specs:

| Component | Minimum Requirement | Recommended |
| :--- | :--- | :--- |
| **OS** | Linux (BlazeOS Debian/Fedora series, Debian 12, Arch , Ubuntu) | BlazeOS Debian/Fedora series |
| **CPU** | 8 Cores / 16 Threads | 16+ Cores (AMD Ryzen / Intel Core i7/i9) |
| **RAM** | 16 GB (+ 16 GB Swap) (zRAM recommended) | 32 GB – 64 GB RAM |
| **Storage** | 400 GB Free Space | 500 GB+ NVMe SSD |
| **Java** | The JDK selected by the Android build environment | Use the bundled/prebuilt JDK |

[**Upgrade to BlazeOS**](https://github.com/DarkMorpheus-pc/Blaze-Galaxy)


## Credits & Acknowledgments

* [**Android Open Source Project (AOSP)**](https://android.googlesource.com)
* [**LineageOS**](https://github.com/LineageOS)
* [**Evolution X**](https://github.com/Evolution-X)
* [**Project Blaze Team**](https://github.com/ProjectBlaze-Staging)
