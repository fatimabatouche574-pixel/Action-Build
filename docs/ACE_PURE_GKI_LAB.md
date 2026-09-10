# OnePlus Ace Pure GKI Lab

This lab is intentionally separate from `ace-builtin-fix.yml`.

The working OnePlus OEM/Built-in SukiSU kernel is left untouched. The lab tests whether OnePlus Ace (MT6895) can move toward a clean Android 12 GKI core instead of merely compiling the OEM tree with `gki_defconfig`.

## Baseline

- Device manifest: `oneplus_ace_v.xml`
- OEM source: `OnePlusOSS/android_kernel_5.10_oneplus_mt6895`
- OEM branch: `oneplus/mt6895_v_15.0.0_ace`
- Target clean GKI tag: `android12-5.10-2025-05_r1`
- Target upstream kernel version: `5.10.236`
- SukiSU integration mode: `main` / GKI mode

## What the workflow does

1. Syncs the original OnePlus Ace kernel tree and MTK vendor modules using the existing device manifest.
2. Preserves the OEM kernel at `kernel-5.10` for comparison.
3. Removes only the manifest-created `kernel_platform/common` symlink.
4. Clones Google's certified Android 12 5.10.236 GKI source into `kernel_platform/common`.
5. Integrates SukiSU using the GKI/main setup path.
6. Builds `gki_defconfig` with KPROBES, MODULES and MODVERSIONS enabled.
7. Builds the clean GKI Image and validates that SukiSU is present.
8. Compares the OEM MTK/Oplus KMI symbol lists against the certified GKI lists and the symbols exported by the built kernel.
9. Uploads the raw Image, `.config`, `Module.symvers`, symbol-diff files and a human-readable compatibility report.

## Flashing policy

A push-triggered run does **not** produce a flashable package. It produces a raw analysis artifact first.

A manual run may request `package_flashable=true`, but the workflow only creates `Ace-PURE-GKI-5.10.236-SukiSU-EXPERIMENTAL.zip` when the static KMI gate reports no missing MTK/Oplus symbols.

Even a passing static gate is not proof that the kernel boots. Runtime compatibility with `vendor_boot`, `vendor_dlkm`, DT/DTBO and vendor modules still requires device-side testing. Keep a known-good boot image and a recovery path before any experimental flash.

## Why this exists

`gki_defconfig` alone does not turn an OEM-modified kernel into a clean Generic Kernel Image. The purpose of this lab is to replace the OEM core with the certified AOSP GKI core while retaining the OnePlus/MTK tree only as the compatibility reference, then quantify the remaining KMI gap before creating a flashable test build.
