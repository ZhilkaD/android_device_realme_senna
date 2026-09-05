# LineageOS 23.2 for Realme GT Neo 5 SE (senna_jr / RMX3700)

![Android](https://img.shields.io/badge/Android-16%20(bp4a)-blue?style=flat-square)
![Status](https://img.shields.io/badge/Build-UNOFFICIAL-orange?style=flat-square)
![SELinux](https://img.shields.io/badge/SELinux-Enforcing-success?style=flat-square)

Unofficial personal port of **LineageOS 23.2 (Android 16)** for the **Realme GT Neo 5 SE** (`senna_jr` / `RMX3700`).  
*Personal port, not affiliated with LineageOS.*

* **Build:** `lineage-23.2-20260904-UNOFFICIAL-senna_jr`
* **Type:** `userdebug`, `TARGET_RELEASE=bp4a`

> **Important Notices:**
> * **Hobby Build:** Signed with the public AOSP testkey. Any APK signed with that key gets platform permissions.
> * **Integrity:** AVB is off (`vbmeta` and `vbmeta_system` flashed with verity/verification disabled). Play Integrity fails, Google Pay and some banking apps will not work.
> * **Security:** adb is secure (`ro.adb.secure=1`, `ro.debuggable=0`) and SELinux is enforcing. No adb root.
> * **Updates:** No OTA or recovery sideload. The stock super metadata carries zero-size ghost `_b` partitions that make `liblp` refuse the install; all updates are manual fastboot flashes.
> * **Kernel:** Stock prebuilt `Image` and vendor modules are used verbatim. Sources: [realme-kernel-opensource](https://github.com/realme-kernel-opensource).

---

## Tested Base (Read Before Flashing)

Exactly one firmware base was tested: **Global RMX3701**:
```text
ro.build.display.id      = RMX3701_16.0.5.1010(EX01)
ro.build.version.ota     = RMX3701_11.H.43_3430_202607021803
ro.boot.prjname          = 22623
ro.product.vendor.device = RE58D1L1
```

Check your parameters in fastboot/adb shell before flashing:
```bash
adb shell getprop ro.boot.prjname
adb shell getprop ro.product.vendor.device
```
> If `prjname` is not `22623` or vendor device is not `RE58D1L1`, do not flash. Chinese ColorOS bases are untested. Firmware variants split into `RE585F` and `RE58D1L1` — only the latter was validated.

---

## Hardware Status

### Working
* Boot and system stability (daily driver ready)
* Display at 144 Hz (measured 4m02s at 144 Hz out of 4m21s screen-on time)
* Touch response
* Speaker and earpiece audio, voice calls (in-call audio works both ways)
* Mobile data & Wi-Fi
* In-display fingerprint (at all brightness levels, including fully dimmed screen)
* Camera stills
* Hardware sensors
* Storage encryption (`/data` is `f2fs` with inline crypto)
* SELinux Enforcing

### Known Issues
* **GPU Composition:** `libsdmextension.so` (closed-source stock SDM extension) has a vtable shifted against LineageOS display sources, crashing composer HAL at init. It is disabled in this build. SDM has no built-in hardware composition fallback without it, so **every layer is composed by the GPU**.
  * Pipeline depth is ~3 frames (minor input delay, but completely smooth with 0 dropped frames).
  * GPU composites at 144 fps even on static screens, increasing power draw and heat. Restoring hardware composition is the main goal for future revisions.

### Not Tested in Detail
* NFC (stock firmware ships a 32-bit `libsn100u_fw.so`)
* Video recording
* Bluetooth audio and headsets
* Wi-Fi hotspot, VoLTE and VoWiFi
* DRM playback (Netflix HD, etc.)
* GNSS accuracy

---

## Installation Guide

### Prerequisites
* Unlocked bootloader.
* Latest Android [platform-tools](https://developer.android.com/studio/releases/platform-tools) extracted to `C:\platform-tools`.
* Firmware archive unpacked directly into `C:\senna`.
* **Full backup:** All data will be wiped.

> **CRITICAL: Back up your `persist` partition first!**  
> It stores unit-specific calibration (fingerprint sensor, Wi-Fi MAC, keys). A lost persist partition cannot be recovered or borrowed.  
> Run from rooted stock or recovery shell:
> ```bash
> dd if=/dev/block/by-name/persist of=/sdcard/persist_backup.img
> ```
> Copy it off the phone and keep it safe. Never flash someone else's persist.

### Step 1: Bootloader Stage
Open `cmd` and initialize paths:
```cmd
set PATH=C:\platform-tools;%PATH%
cd /d C:\senna
fastboot devices
```

Flash core boot images:
```cmd
fastboot --disable-verity --disable-verification flash vbmeta vbmeta.img
fastboot --disable-verity --disable-verification flash vbmeta_system vbmeta_system.img
fastboot flash boot boot.img
fastboot flash dtbo dtbo.img
fastboot flash vendor_boot vendor_boot.img
fastboot flash recovery recovery.img
fastboot reboot fastboot
```
*(Wait 20–30 seconds until the phone reboots into fastbootd).*

### Step 2: fastbootd Stage
Flash dynamic partitions:
```cmd
fastboot flash system system.img
fastboot flash system_ext system_ext.img
fastboot flash product product.img
fastboot flash vendor vendor.img
fastboot flash vendor_dlkm vendor_dlkm.img
fastboot flash odm odm.img
fastboot flash odm_dlkm odm_dlkm.img
```
*(Ignore warnings like `Invalid sparse file format at header magic` on `vendor.img`/`odm.img`).*

### Step 3: Wipe & Boot
```cmd
fastboot erase metadata
fastboot erase userdata
fastboot reboot
```
*First boot takes 2 to 5 minutes.*

---

## How to Revert to Stock

1. In `fastbootd`, flash all seven stock images: `system`, `system_ext`, `product`, `vendor`, `vendor_dlkm`, `odm`, `odm_dlkm`.
2. Flash stock `boot`, `dtbo`, `vendor_boot`, `recovery`, `vbmeta`, `vbmeta_system`, and `vbmeta_vendor` **without** `--disable` flags.
3. Format data and reboot:
```cmd
fastboot erase metadata
fastboot erase userdata
fastboot reboot
```

---

## Credits & Sources

* **etern1ty-crypto** — for the realme senna device tree and OnePlus `sm8450-common` base.
* **The LineageOS Team** — upstream platform sources.
* **Realme / Oplus** — official [kernel releases](https://github.com/realme-kernel-opensource).
