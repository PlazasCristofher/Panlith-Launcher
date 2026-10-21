# Panlith Launcher

Unofficial, **experimental** fork of [Zalith Launcher 2](https://github.com/ZalithLauncher/ZalithLauncher2) for Android.

[English](README.md)

> [!IMPORTANT]
> **Panlith is not Zalith.**  
> This project is an **unofficial modified version** of Zalith Launcher 2.  
> It is **not** affiliated with, endorsed by, or maintained by the Zalith team, Mojang, or Microsoft.  
> For the official launcher, use [ZalithLauncher2](https://github.com/ZalithLauncher/ZalithLauncher2).

**Panlith Launcher** keeps the Zalith 2 / Pojav-based launch core and modern UI, and adds support for loading a **native PanVK** Vulkan driver on **Mali** GPUs through a plugin (**Basalt Driver**)—**without root** and **without replacing** the system Vulkan driver.

```text
Minecraft 26.x  →  LWJGL Vulkan  →  shim  →  PanVK-kbase  →  Mali
OpenGL-era MC   →  Zink/Kopper   →  VULKAN_PTR (shim)  →  PanVK-kbase  →  Mali
Fallback        →  system Vulkan / OpenGL (e.g. MobileGlues)
```

---

## What we add (vs stock Zalith 2)

| Area | Change |
|------|--------|
| Driver plugins | Recognize PanVK-style plugins (metadata / driver flag) |
| Launch environment | Sets `PANVK_SHIM_PATH`, `PANVK_ICD_PATH`, Mesa cache vars, plus `DRIVER_PATH` when PanVK is selected |
| Native Vulkan load | Prefer `dlopen` of the **Basalt shim** when those variables are set; otherwise fall back like upstream |
| `VULKAN_PTR` | Shim library handle for LWJGL and Mesa/Zink |
| System image | **Unchanged** — no root, no bootloader unlock, no OEM driver replacement |

All other launcher features follow upstream Zalith 2.

---

## Requirements

- Android device suitable for Zalith (arm64 recommended)
- **Mali** GPU where [PanVK-kbase](https://github.com/zenithblue-oss/panvk-kbase-android) can run
- **[Basalt Driver](https://github.com/)** plugin (shim + ICD) — *add your plugin repo URL*
- No root required

### Tested configuration

| | |
|--|--|
| Device | Samsung Galaxy A16 5G (SM-A166M) |
| SoC / GPU | Exynos 1330 / **Mali-G68 MC2** |
| Android | 16 |
| Upstream base | Zalith Launcher 2.x (PanVK-patched fork) |

Other devices are **not** guaranteed.

---

## How to use

1. Install **Panlith** (this app). Use a distinct install from official Zalith if both are present (`applicationId` differs).
2. Install **Basalt Driver**.
3. In driver settings, select **PanVK / Basalt**.
4. Choose a renderer:
   - **Minecraft 26.x:** native Vulkan backend.
   - **Older OpenGL titles (e.g. 1.8.9, 1.12):** Kopper Zink / Ironized Zink (or any path with `GALLIUM_DRIVER=zink`).
5. If PanVK fails to load, use OpenGL (e.g. MobileGlues) or the default driver.

### Environment variables (set automatically)

| Variable | Role |
|----------|------|
| `DRIVER_PATH` | Plugin `nativeLibraryDir` |
| `PANVK_SHIM_PATH` | Path to `libvulkan_panvk_shim.so` |
| `PANVK_ICD_PATH` | Path to `libvulkan_panfrost.so` |
| `VULKAN_PTR` | Shim `dlopen` handle (hex), consumed by native code / Zink |
| `MESA_SHADER_CACHE_DIR` | Mesa shader cache when applicable |

### Verified paths (reference device)

| Path | Result |
|------|--------|
| MC **26.x** native Vulkan + PanVK | Works (`panvk` in F3 / logs) |
| MC **1.8.9** Kopper Zink + PanVK | Menu and world |
| Zink + shaders (e.g. 1.12) | Works |
| OpenGL MobileGlues | Reliable fallback |

**Performance note (MC 26.x, ~2340×1080):** OpenGL ≈ 60+ FPS; PanVK at full resolution ≈ 18–20 FPS; lower render scale improves PanVK FPS. PanVK on G68 remains experimental.

---

## Building (developers)

Environment follows upstream Zalith 2 as closely as possible. Typical expectations:

- Android Studio (recent stable)
- Android SDK (see upstream; min/target APIs per Zalith tree you forked)
- JDK as required by the Zalith version you based this on (upstream docs mention JDK 11; some CI setups use newer JDKs—match **your** branch/`build.gradle`)

```bash
git clone --recurse-submodules https://github.com/YOUR_USER/panlith-launcher.git
# Open in Android Studio, or build via the project’s Gradle / GitHub Actions workflow
```

PanVK-related changes live mainly in:

- Launch-time env setup (Kotlin)
- Native Vulkan load path (`egl_bridge` / driver helper family)

App display name and branding: adjust in `gradle.properties` (and resources) so the product is clearly **Panlith**, not Zalith.

More detail on the load path: see the **Panlith + Basalt** documentation repo (or `docs/PANVK_PATH.md` if present).

---

## License

This program is a modification of **Zalith Launcher 2** and is licensed under the **[GNU GPL-3.0](LICENSE)**.

Upstream project: [ZalithLauncher/ZalithLauncher2](https://github.com/ZalithLauncher/ZalithLauncher2).

### Additional terms (from upstream, GPLv3 section 7)

When you distribute a modified version of this program you must:

1. **Distinguish it from the original** in a reasonable way (name and/or version)  
   ([GPLv3 §7(c)](https://github.com/ZalithLauncher/ZalithLauncher2/blob/main/LICENSE)).
   - Do **not** use the name **“ZalithLauncher”**, the abbreviation **“ZL”**, or a name that is confusingly similar to the official product.
   - Show clearly on the **startup screen or main UI** that this is an **unofficial modified version**.
   - The application display name may be changed in `gradle.properties`.

2. **Do not remove** the copyright notices displayed by the program  
   ([GPLv3 §7(b)](https://github.com/ZalithLauncher/ZalithLauncher2/blob/main/LICENSE)).

**Panlith** is an unofficial modified build. Please prefer and support [upstream Zalith Launcher 2](https://github.com/ZalithLauncher/ZalithLauncher2) for general launcher features and issues unrelated to PanVK.

---

## Third-party / open source

This fork **inherits** Zalith Launcher 2’s dependencies (LWJGL, Mesa-related components, AndroidX, etc.).  
For the full upstream library table, see the [Zalith Launcher 2 README](https://github.com/ZalithLauncher/ZalithLauncher2).

**Additionally relevant to Panlith:**

| Component | Role | Project |
|-----------|------|---------|
| PanVK-kbase | Mali Vulkan ICD (kbase) | [zenithblue-oss/panvk-kbase-android](https://github.com/zenithblue-oss/panvk-kbase-android) |
| Basalt shim | ICD ↔ LWJGL/Zink entry points | Panlith + Basalt |
| Mesa / panvk | Driver stack | [mesa3d.org](https://mesa3d.org/) |

Ship only binaries you are allowed to redistribute under their respective licenses.

---

## Acknowledgments

### Upstream (essential)

- **[zenithblue-oss](https://github.com/zenithblue-oss)** — **[panvk-kbase-android](https://github.com/zenithblue-oss/panvk-kbase-android)**  
  PanVK on Android via the Mali **kbase** interface, releases, and the ecosystem that made this experiment possible. Panlith/Basalt **depend** on their work; driver credit belongs to them.
- **[ZalithLauncher](https://github.com/ZalithLauncher/ZalithLauncher2)** — launcher base (GPL-3.0)
- PojavLauncher family — historical launch core
- Mesa / Panfrost / panvk contributors
- PanProbe; community plugins (e.g. PanVKDriverPlugin, FCL Turnip-style drivers); MobileGlues; Kopper-Zink / IronizedZink

### Panlith + Basalt team

- **Cristofher ([PlazasCristofher](https://github.com/PlazasCristofher))** — device bring-up, integration, testing, and project direction  
- All teammates who worked on the Zalith patch, Basalt shim/plugin packaging, and end-to-end validation on real hardware  

### Tools & assistance

- ChatGPT, Claude, Grok, and Copilot — design discussion, research, and implementation support  

Errors and experimental behavior in this fork are the responsibility of the Panlith + Basalt team, not of Zalith or zenithblue-oss.

---

## Disclaimer

Experimental software. It may crash, underperform, or fail on untested devices.  
Use at your own risk. For stable everyday play without PanVK, official Zalith + OpenGL paths are often a better fit until PanVK matures on your GPU.
```

---

**Before you publish**

1. Put real URLs for the Basalt repo and meta docs.  
2. Point `LICENSE` links at the **same commit/branch** you forked if `main` moved.  
3. In the **app UI**, add a visible **“Unofficial modified version of Zalith Launcher 2”** string (required by upstream terms).  
4. Add other human teammates under **Panlith + Basalt team** when you have handles.

This is ready to paste as `README.md` (English-first).
