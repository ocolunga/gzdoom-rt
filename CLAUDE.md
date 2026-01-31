# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

GZDoom: Ray Traced — a fork of GZDoom (Doom source port) that adds a path-tracing renderer via the RTGL1 library. Licensed under GPLv3. Primary platform is Windows; Linux and macOS are supported but without ray tracing.

## Build Commands

### Windows (primary)

Full automated build (clones vcpkg + ZMusic, configures, and builds):
```
auto-setup-windows.cmd
```
This produces `build/RelWithDebInfo/gzdoom.exe` and `build/GZDoom.sln`.

Manual CMake build:
```
cmake -A x64 -S . -B build -DCMAKE_TOOLCHAIN_FILE=build/vcpkg/scripts/buildsystems/vcpkg.cmake -DZMUSIC_INCLUDE_DIR=build/zmusic/include -DZMUSIC_LIBRARIES=build/zmusic/build/source/Release/zmusic.lib
cmake --build build --config RelWithDebInfo -- -maxcpucount -verbosity:minimal
```

To run the built executable, you must copy from a release package into `build/RelWithDebInfo/`: the `rt` folder, `libsndfile-1.dll`, `openal32.dll`, and `zmusic.dll`.

### Linux
```
sudo apt install libsdl2-dev libvpx-dev libwebp-dev
mkdir build && cd build
cmake .. -DZMUSIC_INCLUDE_DIR=<path> -DZMUSIC_LIBRARIES=<path>
cmake --build . --parallel
```

### Key CMake Options

- `HAVE_RT` (default ON) — enables ray tracing, requires C++20. When OFF, uses C++17.
- `HAVE_VULKAN` — enables Vulkan renderer
- `HAVE_GLES2` — enables GLES2 renderer
- `ZDOOM_ENABLE_SWR` — enables software renderer (default ON)
- vcpkg triplet is forced to `x64-windows-static` on Windows

## Architecture

### Rendering Backends

The engine supports multiple rendering backends under `src/common/rendering/`:
- **`rt/`** — Ray tracing renderer (RTGL1 integration). The core RT-specific code. Uses C++20 features (concepts, structured bindings). Has its own `.clang-format`.
- **`gl/`** — OpenGL renderer
- **`gles/`** — GLES2 renderer
- **`vulkan/`** — Vulkan renderer
- **`hwrenderer/`** — Shared hardware rendering base used by all GPU backends

Key RT files:
- `rt_main.cpp` — primary RT implementation (~4600 lines), scene submission to RTGL1
- `rt_state.h` — `FRtState` class, manages RT rendering state with RAII `AutoPop` helpers
- `rt_cvars.h` — console variables controlling RT behavior (DLSS, FSR2/FSR3, frame generation, HDR)
- `rt_helpers.h` — utility templates and helpers

### Core Engine (`src/common/`)

- `engine/` — core engine logic
- `scripting/` — ZScript virtual machine (ZDoom's scripting language)
- `filesystem/` — WAD/PK3 resource file management
- `textures/` — texture loading and management
- `models/` — 3D model loading
- `audio/` — sound system
- `2d/` — 2D rendering, UI drawing
- `console/` — in-game console
- `menu/` — menu system
- `platform/` — platform abstraction (POSIX, Win32)

### Game Logic (`src/`)

Top-level `src/` contains Doom-specific game code:
- `d_main.cpp` — main entry point
- `g_game.cpp` — game state management
- `p_setup.cpp` — level loading
- `p_tick.cpp` — game tick processing

### Resource Packages

PK3 archives are built from `wadsrc*/` directories using the custom `tools/zipdir` tool:
- `wadsrc/` — core game resources
- `wadsrc_bm/` — BroadcastMatch resources
- `wadsrc_lights/` — lighting definitions
- `wadsrc_extra/` — extra resources
- `wadsrc_widepix/` — high-resolution assets

### Third-Party Libraries

Internal (`libraries/`): asmjit (JIT), bzip2, lzma, miniz, ZVulkan, Discord RPC.
External (via vcpkg or system): SDL2, OpenAL, ZMusic, libvpx, libwebp, BZip2.

## Coding Conventions

### RT code (`src/common/rendering/rt/`)

Has its own `.clang-format` (Microsoft-based style):
- 4-space indentation, no tabs
- 100-character column limit
- Spaces inside angle brackets, parentheses, and square brackets
- `NamespaceIndentation: Inner`
- `PointerAlignment: Left`
- `AlwaysBreakTemplateDeclarations: Yes`
- `BreakConstructorInitializers: BeforeComma`

### General codebase

- The wider GZDoom codebase does not have a unified formatter — follow the style of surrounding code
- Windows builds define `RAYTRACED` and `HAVE_RT=1`
- Precompiled headers are used (see `cmake/precompiled_headers.cmake`)
- MSVC flags: `/permissive-`, `/fp:fast`, SSE2 minimum
- GCC/Clang: `-ffp-contract=off` for float precision

## CI

GitHub Actions (`.github/workflows/continuous_integration.yml`) runs on push and PR:
- Windows: VS 2022 (Release, Debug), VS 2019 (Release)
- macOS: macOS 12 (Release, Debug)
- Linux: GCC 9/12, Clang 11/15 across various build types

There is no automated test suite — CI validates that the project compiles across all platform/compiler combinations.
