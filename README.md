# MSI GF75 Thin 9SC Hackintosh — macOS Tahoe

OpenCore EFI for the **MSI GF75 Thin 9SC** (Coffee Lake-H), tested on **macOS Tahoe 26**.

## Screenshot

<img width="1920" height="1080" alt="macOS Tahoe 26.6.1 running on MSI GF75 Thin 9SC" src="https://github.com/user-attachments/assets/d534b825-e79a-41c9-99ef-b20fe598cdf3" />

*macOS Tahoe 26.6.1 on MSI GF75 Thin 9SC — Intel Core i7-9750H, Intel UHD 630, SMBIOS `MacBookPro16,1`.*

> [!IMPORTANT]
> This repository is **not a drop-in EFI**. SMBIOS identity values have intentionally been removed. Generate your own Serial, MLB, SystemUUID and ROM before booting this EFI on your machine.

## Hardware

| Component | Model / Notes | Status |
|---|---|---|
| Laptop | MSI GF75 Thin 9SC-477VN | ✅ |
| CPU | Intel Core i7-9750H | ✅ |
| iGPU | Intel UHD Graphics 630 | ✅ acceleration / brightness |
| dGPU | NVIDIA GeForce GTX 1650 | ❌ unsupported, disabled |
| Internal display | BOE0823 1920×1080 | ✅ |
| HDMI | Routed through Intel UHD 630 | ✅ |
| Audio | Realtek ALC269 | ✅ with Tahoe Modern Audio root patch |
| Wi-Fi | Intel Wireless-AC 9560 | ✅ via itlwm + HeliPort |
| Bluetooth | Intel Bluetooth | ✅ |
| Ethernet | Realtek RTL8168/8111 | ✅ |
| Touchpad | I2C HID | ✅ |
| Keyboard | PS/2 | ✅ |
| NVMe | WD PC SN520 | ✅ |
| USB / USB-C | Custom USB map | ✅ |
| Sleep / Wake | — | ✅ |

## Tested

- macOS Tahoe 26
- OpenCore 1.0.8 configuration
- SMBIOS: `MacBookPro16,1`
- Graphical OpenCanopy boot picker
- Dual boot with Windows supported (keep your existing `EFI/Microsoft` directory)

## Before you boot

Generate **your own** SMBIOS values for `MacBookPro16,1` and replace these fields in `EFI/OC/config.plist`:

```text
PlatformInfo -> Generic -> SystemSerialNumber
PlatformInfo -> Generic -> MLB
PlatformInfo -> Generic -> SystemUUID
PlatformInfo -> Generic -> ROM
```

The public config intentionally contains placeholders:

```text
SystemSerialNumber = GENERATE-YOUR-OWN
MLB                = GENERATE-YOUR-OWN
SystemUUID         = 00000000-0000-0000-0000-000000000000
ROM                = 000000000000
```

Do **not** reuse another person's SMBIOS identity.

## Tahoe audio / root patch

Built-in ALC269 audio on Tahoe requires the **Modern Audio** post-install root patch. This EFI uses the AMFIPass path (`AMFIPass.kext` + `-amfipassbeta`); **do not add `amfi=0x80`**.

The tested patch flow is:

```text
OpenCore-Patcher
  -> Post-Install Root Patch
  -> Miscellaneous: Modern Audio
```

The Tahoe patcher used and tested for this EFI is **[kgp-macPro/OCLP-lzhoang2801-amfipassbeta](https://github.com/kgp-macPro/OCLP-lzhoang2801-amfipassbeta)**. Use OCLP for the **root patch only** — do not let it rebuild or replace this machine-specific EFI. FileVault may need to be disabled, a KDK/support package may be required, and macOS updates can require the Modern Audio patch to be applied again.

Read **[PATCHING.md](PATCHING.md)** before applying any Tahoe root patch. It includes the tested AMFIPass patcher release, checksum verification, FileVault notes, expected patch set, post-patch checks and update/recovery guidance.

## Wi-Fi

Wi-Fi uses `itlwm.kext`, so use **HeliPort** to connect to wireless networks. This is not Apple's native Wi-Fi stack, therefore native continuity features such as AirDrop should not be assumed to work.

## USB map

The included `USBMap.kext` is specific to this machine/configuration. Tahoe USB enumeration was verified with internal Bluetooth, webcam, external USB devices and USB-C working.

## Boot picker

OpenCanopy is enabled for a normal graphical boot experience. Auxiliary entries are hidden by default. Press **Space** in the picker to reveal entries such as Recovery and Reset NVRAM.

## Installation

1. Back up your current EFI.
2. Generate and insert your own SMBIOS identity values.
3. Copy `EFI/BOOT` and `EFI/OC` to your EFI System Partition.
4. If dual booting Windows, **do not delete `EFI/Microsoft`**.
5. Keep a known-good rescue USB before making changes to your internal EFI.

## Important notes

- This EFI is tailored to this hardware revision. Similar GF75 models may have different panels, audio codecs, Wi-Fi cards, USB port layouts, or firmware behaviour.
- BIOS settings and firmware revisions can affect boot behaviour.
- The GTX 1650 is not used by macOS.
- Root patches modify the macOS system volume and are separate from the EFI itself.
- Use at your own risk and keep backups.

## Credits

This EFI builds on work from the OpenCore/Hackintosh ecosystem. In particular:

- **[lzhoang2801/OpCore-Simplify](https://github.com/lzhoang2801/OpCore-Simplify)** — used as the OpenCore configuration/building foundation before machine-specific Tahoe fixes and validation.
- **[kgp-macPro/OCLP-lzhoang2801-amfipassbeta](https://github.com/kgp-macPro/OCLP-lzhoang2801-amfipassbeta)** — maintained Tahoe AMFIPass OCLP fork used for the Modern Audio / AppleHDA root patch.
- **Acidanthera / OpenCorePkg** — OpenCore bootloader and core ecosystem.
- Lilu, WhateverGreen, AppleALC, VirtualSMC and related Acidanthera projects.
- VoodooI2C.
- OpenIntelWireless (`itlwm`, IntelBluetoothFirmware).
- RealtekRTL8111.
- Dortania / OpenCore Legacy Patcher contributors and the wider Hackintosh community.

Please credit the upstream projects above if you redistribute or build on this EFI. Third-party binaries remain subject to their respective upstream licenses.
