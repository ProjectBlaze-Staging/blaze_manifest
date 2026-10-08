# BlazeAOSP (Android 17 / ProjectBlaze 5)

---

## Building Blaze !

### 1. Initialize the Source Repository
```bash
# Create BlazeAOSP directory
mkdir -p ~/blaze && cd ~/blaze

# Initialize BlazeAOSP 17.0 manifest
repo init -u https://github.com/ProjectBlaze-Staging/blaze_manifest.git -b 17.0
```

### 2. Sync the Source Code
```bash
repo sync -c --no-clone-bundle --no-tags --optimized-fetch --prune --force-sync -j$(nproc --all)
```

### 3. Build the System
```bash
# Set up build environment
source build/envsetup.sh

#set POSIX standarts
export LC_ALL=C

# Select your device target (replace <device_codename> with your device e.g. bluejay, cheetah, etc.)
lunch lineage_<device_codename>-cp2a-userdebug

# Start compilation
m bacon -j$(nproc --all)
```

---

## Hardware & Build Requirements

To build BlazeAOSP from source, your workstation should meet the following minimum specs:

| Component | Minimum Requirement | Recommended |
| :--- | :--- | :--- |
| **OS** | Linux (BlazeOS Debian/Fedora series, Debian 12, Arch , Ubuntu) | BlazeOS Debian/Fedora series |
| **CPU** | 8 Cores / 16 Threads | 16+ Cores (AMD Ryzen / Intel Core i7/i9) |
| **RAM** | 16 GB (+ 16 GB Swap) (zRAM recommended) | 32 GB – 64 GB RAM |
| **Storage** | 400 GB Free Space | 500 GB+ NVMe SSD |
| **Java** | OpenJDK 17 | OpenJDK 17 |

[**Upgrade to BlazeOS**](https://github.com/DarkMorpheus-pc/Blaze-Galaxy)


## Credits & Acknowledgments

* [**Android Open Source Project (AOSP)**](https://android.googlesource.com)
* [**LineageOS**](https://github.com/LineageOS)
* [**Evolution X**](https://github.com/Evolution-X)
* [**Project Blaze Team**](https://github.com/ProjectBlaze-Staging)
