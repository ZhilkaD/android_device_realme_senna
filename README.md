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
