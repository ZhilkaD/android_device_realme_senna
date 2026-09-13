# LineageOS 23.2 for Realme GT Neo 5 SE (senna_jr / RMX3700)

![Android](https://img.shields.io/badge/Android-16%20(bp4a)-blue?style=flat-square)
![Status](https://img.shields.io/badge/Build-UNOFFICIAL-orange?style=flat-square)
![SELinux](https://img.shields.io/badge/SELinux-Enforcing-success?style=flat-square)

Unofficial personal port of **LineageOS 23.2 (Android 16)** for the **Realme GT Neo 5 SE** (`senna_jr` / `RMX3700`).  
*Personal port, not affiliated with LineageOS.*

* **Build:** `lineage-23.2-20260913-UNOFFICIAL-senna_jr`
* **Type:** `userdebug`, `TARGET_RELEASE=bp4a`

> **Important Notices:**
> * **Hobby Build:** Signed with the public AOSP testkey. Any APK signed with that key gets platform permissions.
> * **Integrity:** AVB is off (`vbmeta` and `vbmeta_system` flashed with verity/verification disabled). Play Integrity fails, Google Pay and some banking apps will not work.
> * **Security:** adb is secure (`ro.adb.secure=1`, `ro.debuggable=0`) and SELinux is enforcing. No adb root.
> * **Updates:** No OTA or recovery sideload. All updates are manual fastboot flashes or via Fastboot Firmware Flasher.
> * **Kernel:** Stock prebuilt `Image` and vendor modules are used verbatim. Sources: [realme-kernel-opensource](https://github.com/realme-kernel-opensource).

---

## Changelog — 2026-09-13

* **Fixed: Wi-Fi hotspot** — previously shut down ~1 second after enabling
* **Fixed: USB tethering** — internet sharing via cable was not working
* Root cause: `dnsmasq` failed to start due to `POSIX_SPAWN_CLOEXEC_DEFAULT` requiring Linux 5.11+, while this device runs kernel 5.10.236.

---

## Tested Base (Read Before Flashing)

Exactly one firmware base was tested: **Global RMX3701**:
```text
ro.build.display.id      = RMX3701_16.0.5.1010(EX01)
ro.build.version.ota     = RMX3701_11.H.43_3430_202607021803
ro.boot.prjname          = 22623
ro.product.vendor.device = RE58D1L1
```

Check your parameters before flashing:
```bash
adb shell getprop ro.boot.prjname
adb shell getprop ro.product.vendor.device
```
> If `prjname` is not `22623` or vendor device is not `RE58D1L1`, do not flash. Chinese ColorOS bases are untested.

---

## Hardware Status

### Working
* Boot and system stability (daily driver ready)
* Display at 144 Hz (real, measured)
* Touch response
* Speaker and earpiece audio, voice calls (VoLTE)
* Mobile data & Wi-Fi
* **Wi-Fi hotspot and USB tethering** (fixed in this build)
* In-display fingerprint (at all brightness levels)
* All 4 cameras, video recording
* NFC (payments work)
* Bluetooth audio and headsets
* GPS / satellite navigation
* DRM video (Netflix etc.)
* Hardware sensors, vibration
* SELinux Enforcing

### Known Issues
* **GPU Composition:** All UI layers are composed by the GPU (Oplus hardware composer incompatible with our display stack).
  * Minor input delay, completely smooth with 0 dropped frames.
  * Higher power draw. Restoring hardware composition is the main goal for v2.

---

## Installation Guide

### Prerequisites
* Unlocked bootloader.
* Latest Android [platform-tools](https://developer.android.com/studio/releases/platform-tools) extracted to `C:\platform-tools`.
* Firmware archive unpacked to `C:\senna`.
* **Full backup:** All data will be wiped.

### Option 1: Fastboot Firmware Flasher (recommended)

1. Download Fastboot Firmware Flasher
2. Copy folder `LineageOS_23.2_20260913_senna_jr` into FFF's `FIRMWARE` folder
3. Open FFF → Flasher → select the firmware
4. Enable "Disable VBMeta verification"
5. Connect phone, click "To FastbootD" → "Start"

### Option 2: flash.bat

1. Extract archive to `C:\senna`
2. Power off → Volume Down + Power (bootloader)
3. Run `flash.bat`

### Option 3: Manual commands

**Bootloader stage:**
```cmd
set PATH=C:\platform-tools;%PATH%
cd /d C:\senna
fastboot --disable-verity --disable-verification flash vbmeta vbmeta.img
fastboot --disable-verity --disable-verification flash vbmeta_system vbmeta_system.img
fastboot flash boot boot.img
fastboot flash dtbo dtbo.img
fastboot flash vendor_boot vendor_boot.img
fastboot flash recovery recovery.img
fastboot reboot fastboot
```

**fastbootd stage:**
```cmd
fastboot flash system system.img
fastboot flash system_ext system_ext.img
fastboot flash product product.img
fastboot flash vendor vendor.img
fastboot flash vendor_dlkm vendor_dlkm.img
fastboot flash odm odm.img
fastboot flash odm_dlkm odm_dlkm.img
fastboot erase metadata
fastboot erase userdata
fastboot reboot
```

*First boot takes 2–5 minutes.*

---

## How to Revert to Stock

1. Flash all seven stock dynamic partition images in fastbootd.
2. Flash stock `boot`, `dtbo`, `vendor_boot`, `recovery`, `vbmeta`, `vbmeta_system`, `vbmeta_vendor` **without** `--disable` flags.
3. `fastboot erase metadata && fastboot erase userdata && fastboot reboot`

---

## Credits & Sources

* **[LolD:O](https://4pda.to/forum/index.php?showuser=9090479)** — original device tree (`senna_jr`) and OnePlus `sm8450-common` base.
* **The LineageOS Team** — upstream platform sources.
* **Realme / Oplus** — official [kernel releases](https://github.com/realme-kernel-opensource).
