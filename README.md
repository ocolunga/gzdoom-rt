# GZDoom: Ray Traced

**GZDoom: Ray Traced** is a fork of [GZDoom](https://zdoom.org/) that adds a real-time path tracing renderer via the [RTGL1](https://github.com/sultim-t/RTGL) library. It replaces the traditional rasterized rendering with full ray-traced global illumination, reflections, refractions, and shadows.

## Project Goals

- **Doom 1 RT support** — Extend the ray tracing renderer to support Ultimate Doom (currently Doom II only), including per-episode lighting and scene data
- **Hand-crafted scene data** — Improve lighting quality beyond auto-exported defaults with hand-tuned light placement, materials, and effects for iconic maps
- **Upstream GZDoom merge** — Eventually re-port the RT renderer onto a modern GZDoom base to benefit from ongoing engine improvements

## Current Status

- Based on **GZDoom 4.12pre** (engine version 4.12.0)
- **Doom II** is fully supported with hand-crafted RT lighting
- **Doom 1** support is in progress (launcher enabled, basic RT rendering works, scene data being developed)
- Primary platform is **Windows** (x64); Linux and macOS compile but without ray tracing

## Building

### Windows (primary)

**Prerequisites:** Visual Studio 2022 with "Desktop development with C++" workload, CMake 3.16+, Git. Set the `RTGL1_SDK_PATH` environment variable to a built [RTGL1 SDK](https://github.com/sultim-t/RTGL).

1. Run `auto-setup-windows.cmd` (clones vcpkg + ZMusic, configures CMake, builds)
   - Produces `build/RelWithDebInfo/gzdoom.exe` and `build/GZDoom.sln`
2. Copy from a [release package](https://github.com/vs-shirokii/gzdoom-rt/releases) into `build/RelWithDebInfo/`:
   - `rt/` folder (RTGL1 binaries and scene data)
   - `libsndfile-1.dll`, `openal32.dll`, `zmusic.dll`
3. Run `build/RelWithDebInfo/gzdoom.exe` with a Doom IWAD (`DOOM2.WAD` or `DOOM.WAD`)

### Linux

```bash
sudo apt install libsdl2-dev libvpx-dev libwebp-dev
mkdir build && cd build
cmake .. -DZMUSIC_INCLUDE_DIR=<path> -DZMUSIC_LIBRARIES=<path>
cmake --build . --parallel
```

Note: Ray tracing (`HAVE_RT`) requires the RTGL1 SDK and is Windows-only for now. Linux builds fall back to the standard GZDoom renderers.

## Credits

### Ray Tracing Renderer
- **Vasilii Shirokii** ([@vs-shirokii](https://github.com/vs-shirokii)) — Created the RT renderer integration and RTGL1-based path tracing pipeline for GZDoom

### RTGL1 Library
- **Sultim Tsyrendashiev** ([@sultim-t](https://github.com/sultim-t)) — Author of the [RTGL1](https://github.com/sultim-t/RTGL) ray tracing library

### GZDoom
- **Christoph Oelckers** (Graf Zahl) and the [ZDoom/GZDoom team](https://github.com/ZDoom/gzdoom) — The source port this fork is based on

### Original Doom
- **id Software** (John Carmack, John Romero, et al.) — Doom engine and game (1993)

## License

Licensed under the [GNU General Public License Version 3](LICENSE).

Copyright (c) 1998-2023 ZDoom + GZDoom teams, and contributors.
Doom Source (c) 1997 id Software, Raven Software, and contributors.
Please see license files for individual contributor licenses.

---

[GZDoom Home](https://zdoom.org/) | [GZDoom Wiki](https://zdoom.org/wiki/) | [ZDoom Discord](https://dsc.gg/zdoom)
