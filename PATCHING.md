# macOS Tahoe root patch notes

This EFI boots macOS Tahoe, but the built-in **Realtek ALC269** audio path still needs a post-install **Modern Audio** root patch to restore AppleHDA on Tahoe.

> [!WARNING]
> Root patching modifies the macOS System Volume. It is separate from the EFI and can be reverted or invalidated by macOS updates. Keep a bootable rescue USB and a backup before patching.

## AMFI mode used by this EFI

This configuration is intentionally built around:

```text
AMFIPass.kext
-amfipassbeta
```

Do **not** mix this with:

```text
amfi=0x80
```

Using a patcher build intended for a different AMFI mode can cause confusing failures.

## Tested Tahoe patcher

The setup was tested with the maintained Tahoe AMFIPass fork:

- Repository: **[kgp-macPro/OCLP-lzhoang2801-amfipassbeta](https://github.com/kgp-macPro/OCLP-lzhoang2801-amfipassbeta)**
- Release: `3.0.0-amfipassbeta-tahoe`
- Asset: `OpenCore-Patcher.pkg`
- SHA-256: `d2241823e557c53a74099fbc7100cf233e2b3d7a27de5a2ccca1a593ce06341f`

This is a **third-party Hackintosh-oriented fork**, not an official OpenCore Legacy Patcher release. Review the source/release information yourself and use it at your own risk.

Verify the downloaded package before installing:

```bash
shasum -a 256 ~/Downloads/OpenCore-Patcher.pkg
pkgutil --check-signature ~/Downloads/OpenCore-Patcher.pkg
spctl -a -vv -t install ~/Downloads/OpenCore-Patcher.pkg
```

A matching SHA-256 only proves that the file is byte-for-byte the published release asset; it is not, by itself, a malware guarantee.

## Before root patching

1. Boot Tahoe using this EFI and confirm the machine is otherwise stable.
2. Ensure the EFI contains `AMFIPass.kext` and boot arg `-amfipassbeta`.
3. Keep `amfi=0x80` **absent**.
4. Disable FileVault before patching if OCLP reports it as a blocker. Wait until:

   ```bash
   fdesetup status
   ```

   reports `FileVault is Off.`
5. Keep AC power connected.
6. Allow the patcher to obtain the required KDK/support files if requested.

## What to patch

Open:

```text
OpenCore-Patcher
  -> Post-Install Root Patch
```

For this machine, the expected available patch is:

```text
Miscellaneous: Modern Audio
```

> [!IMPORTANT]
> If the patcher unexpectedly proposes graphics, Wi-Fi, or other unrelated root patches, stop and inspect the configuration instead of blindly applying everything.

Apply **Modern Audio only**.

Do **not** use OCLP's `Build and Install OpenCore` flow for this repository. This repo already contains the machine-specific OpenCore EFI; OCLP is only being used for the Tahoe System Volume audio patch.

## After patching

Reboot using this EFI and verify:

```bash
ls -ld /System/Library/Extensions/AppleHDA.kext
kmutil showloaded | grep -Ei "AppleALC|AppleHDA"
system_profiler SPAudioDataType
```

Expected results include loaded `AppleALC`, `AppleHDAController`, `AppleHDA`, and built-in input/output devices.

## After macOS updates

A Tahoe update can replace the patched System Volume. If internal speakers/mic disappear after an update but the EFI still boots normally, re-open the patcher and re-apply:

```text
Post-Install Root Patch -> Modern Audio
```

Do not immediately assume the EFI, layout-id, or AppleALC configuration is broken.

## FileVault

Root-patched systems and FileVault can be a fragile combination. Do not re-enable FileVault until you are satisfied that the current Tahoe version, root patch, reboot, sleep/wake and recovery workflow are all stable for your machine.

## Patch / build credits

- **[kgp-macPro/OCLP-lzhoang2801-amfipassbeta](https://github.com/kgp-macPro/OCLP-lzhoang2801-amfipassbeta)** — Tahoe AMFIPass OCLP fork and AppleHDA-capable patch environment used by this setup.
- **[lzhoang2801/OpCore-Simplify](https://github.com/lzhoang2801/OpCore-Simplify)** — OpenCore configuration/building foundation used for this EFI before the machine-specific Tahoe patches documented in this repository.

These are third-party projects. This repository is not affiliated with or officially supported by their maintainers.
