# Panlith Launcher

[Original Zalith Launcher 2 README](../README_EN_US.md) · [原版 README](../README.md)

> [!IMPORTANT]
> **Panlith Launcher is an unofficial, modified fork of [Zalith Launcher 2](https://github.com/ZalithLauncher/ZalithLauncher2).**
> It is not affiliated with, endorsed by, or maintained by the Zalith Launcher team, Mojang, or Microsoft.
> The official project is ZalithLauncher/ZalithLauncher2. If you are looking for the original, get it from there.

**Panlith Launcher** is a modified version of Zalith Launcher 2, a launcher for [Minecraft: Java Edition](https://www.minecraft.net/) on **Android** that uses PojavLauncher as its core, with a Jetpack Compose and Material Design 3 interface.

The only purpose of this fork is to allow a native **PanVK** Vulkan driver on Mali GPUs, loaded from a plugin ([**Basalt Driver**](https://github.com/PlazasCristofher/Panlith)), **without root, without unlocking the bootloader, and without touching the system Vulkan driver**. If PanVK is not available, the launcher keeps working like Zalith, using OpenGL (MobileGlues).

## What changes compared to Zalith Launcher 2

- Loads a PanVK driver from a plugin (environment variables `DRIVER_PATH`, `PANVK_SHIM_PATH` and `PANVK_ICD_PATH`; the native code loads the shim first).
- The Vulkan checker accepts the PanVK driver.
- Own app name, icons and application ID (`com.d4urw.panlithlauncher`). It is a separate app and does not share data with Zalith.
- "Unofficial Modified Version" notice on the start screen and in About.
- Update checker disabled, because it queried the official Zalith release info.
- When no official signing keys are present, the release build is signed with the debug key.

Everything else is Zalith Launcher 2 code.

## Status

- Tested only on a Samsung Galaxy A16 5G (Mali-G68 MC2, Android 16).
- Minecraft 26.x with native Vulkan: works.
- OpenGL over Kopper/Zink with PanVK: tested on Minecraft 1.8.9.
- Experimental: performance is still lower than OpenGL/MobileGlues.
- No own CurseForge or Microsoft keys: CurseForge downloads and Microsoft sign-in are not enabled. Modrinth needs no key.

## Building (developers)

Requires JDK 21 and the Android SDK (minimum API 26). It is built by the **Panlith release arm64** workflow, which runs on pushes to the `panlith` branch; the APK is published under Artifacts.

## License

The code is licensed under **[GPL-3.0](../LICENSE)**, like the original project, together with its additional terms (GPLv3, section 7). This is how this fork complies:

1. **Different name and modified-version label.** The name does not include "ZalithLauncher", "ZL" or anything similar. The app shows "Unofficial Modified Version" on the start screen and in About. The name is set in `ZalithLauncher/gradle.properties`.
2. **Copyright notices kept intact.** The copyright notices shown by the app and those in each file (MovTery and contributors) are preserved.

## Third-party libraries

Panlith Launcher uses the same open-source libraries as Zalith Launcher 2. The full list with their copyrights and licenses is in the [original README](../README_EN_US.md#open-source-libraries-and-licenses) and is kept unchanged.

## Credits

Zalith Launcher 2 (MovTery and contributors) and PojavLauncher · PanVK-kbase (zenithblue-oss), Mesa and Panfrost · PanProbe · PanVKDriverPlugin (muhibiscr1t1kal) · MobileGlues · Kopper-Zink (whaltermc) · IronizedZink · FCL Turnip plugin.

Made by one person and several AIs: ChatGPT (main ideas), Claude (code and builds), Grok (deep research) and Copilot (support).
