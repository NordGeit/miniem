# nanoem
[![License: MPL 2.0](https://img.shields.io/badge/License-MPL%202.0-blue.svg)](https://opensource.org/licenses/MPL-2.0) [![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://opensource.org/licenses/MIT)

miniem is a fork of nanoem, an [MMD (MikuMikuDance)](https://sites.google.com/view/vpvp/) compatible implementation and its like application mainly built for macOS.

- [All Releases](https://github.com/hkrn/nanoem/releases)
- [Usage Manual](https://nanoem.readthedocs.io) (Japanese)

## Screenshot

![nanoem for macOS](docs/images/application/screen_v340_small.png)

## Background and Design Concept

MikuMikuDance (a.k.a MMD) is created on Windows via DirectX9. As such, it's unavailable to run on non-Windows deployment unless you use a virtual machine or emulation layer such as Wine.

nanoem was originally created to address the issue of non-Windows compatibility as well as the possibility it may not work in the future due to Windows reasons. It was originally designed for macOS, but now it's now designed to run on non-macOS as well and supports ARM CPUs such as Apple M1 and RaspberryPi as well as multiple graphics backends (DirectX11/Metal/OpenGL).

It also has model editing feature similar to PMXEditor's one which allows for some model editing. This was also implemented for the same reason mentioned above.

nanoem is designed to achieve these of the following goals.

* Portability
* Lightweight startup
* Small size

miniem aims to trade off some of the startup time and size in order to add more features.

### As of currently, this project is sleeping. Mostly because I don't know how to code (yet).
### But what's the wishlist here? What makes one change the name of this project and sacrifice some of the above goals?

* DX9 support alongside DX11, and Vulkan support too
* A new system that allows you to dive into the graphics API to make effects and change the rendering more directly instead of relying on the MME system (DirectX Directing, Metal Forging etc.),
* Particle system to reduce further reliance on MME,
* Implementing the functions of some MMD plugins like [Effekseer support](https://github.com/oigami/EffekseerForMMD) and [MMAccel](https://github.com/NordGeit/MMAccel) (original died, here's my fork) natively,
* Breaking the constraints of MMD even further by allowing non-typical keyframes per second for more (or less) precise control (24 KFPS, 60KFPS, etc. instead of the classic 30KFPS) (will need new type of project file for these!),
* Keyframes without interpolation (think the Puss In Boots 2 fight scenes),
* Built-in support to turn rotation bones into movement bones while animating without changing the model itself, to allow for more flexible animation,
* Ability to quickly make IK out of any sets of bones, with some defaults (non-destructively adding IK arms, for example) and switching between the two smoothly,
* Ability to make curves and tracks for the camera and movement bones to follow for smoother movement,
* Moving UI elements around,
* Borrowing some fluid simulation code

That's quite the wishlist. I'll learn more about the technical aspects of each of these as I begin to work, the most optional one would probably be the DX9 support if DX11 picks up the slack and works with the MME just fine. But I believe all of the changes to the animation system would bring about perhaps the biggest QoL to making "MMD" animations and make it able to butt heads with other 3D programs.

While still keeping the simplicity that made MMD easy to learn.

## How to build?

### Prerequisites

- [cmake](https://cmake.org) (>= 3.5)
- C++14 compliant compiler
  - confirmed on Clang and Visual Studio 2017
- [git](https://git-scm.com)
- [ninja-build](https://ninja-build.org/)
  - Optional but recommend on macOS/Linux

### Minimum build instruction

See also [GitHub Action Workflow](.github/workflows/main.yml).

```bash
git submodule update --init --recursive

# needs setting NANOEM_TARGET_COMPILER explicitly when the compiler is clang (default is gcc on Linux)
export NANOEM_TARGET_COMPILER=clang
cmake -P scripts/build.cmake
mkdir out
cd out
cmake -G Ninja ..
cmake --build .

# OpenGL 3.3 Core Profile or higher is required to run on Linux
cd sapp && ./nanoem
```

<details>
<summary>Options Table of scripts/build.cmake</summary>

|Name|Description|
|---|---|
|`NANOEM_TARGET_ARCHITECTURES`|Target architectures to build. The default value is `x86_64` (non macOS) or `ub` (means Universal Binary, macOS).|
|`NANOEM_TARGET_CONFIGURATIONS`|Target configurations for cmake. The default value is `debug;release`.|
|`NANOEM_TARGET_MACOS_VERSION`|Target macOS version. Same as [CMAKE_OSX_DEPLOYMENT_TARGET](https://cmake.org/cmake/help/latest/variable/CMAKE_OSX_DEPLOYMENT_TARGET.html).|
|`NANOEM_TARGET_GENERATOR`|Target generator for cmake. The option will pass as `cmake -G ${NANOEM_TARGET_GENERATOR}`|
|`NANOEM_TARGET_COMPILER`|Target compiler for cmake.|
|`NANOEM_TARGET_TOOLSET`|Target toolset for cmake. The option will pass as `cmake -T {NANOEM_TARGET_TOOLSET}`|
|`NANOEM_MAKE_PROGRAM`|Make program to use for cmake. Same as [CMAKE_MAKE_PROGRAM](https://cmake.org/cmake/help/latest/variable/CMAKE_MAKE_PROGRAM.html).|
|`NANOEM_DISABLE_BUILD_NANOMSG`|Disable building [nanomsg](https://nanomsg.org/).|
|`NANOEM_DISABLE_BUILD_TBB`|Disable building [Threading Building Blocks](https://www.intel.com/content/www/us/en/developer/tools/oneapi/onetbb.html).|
|`NANOEM_ENABLE_BUILD_ICU4C`|Enable building [ICU](https://github.com/unicode-org/icu/). You must run following command `git clone https://github.com/unicode-org/icu/ dependencies/icu` before enabling the option.|
|`NANOEM_ENABLE_BUILD_MIMALLOC`|Enable building [mimalloc](https://github.com/microsoft/mimalloc).|
|`NANOEM_ENABLE_BUILD_SPIRV_TOOLS`|Enable building [SPIRV-Tools](https://github.com/KhronosGroup/SPIRV-Tools).|
|`NANOEM_ENABLE_BUILD_SENTRY_NATIVE`|Enable building [Sentry C/C++ SDK](https://github.com/getsentry/sentry-native).|
|`NANOEM_ENABLE_BUILD_LIBSOUNDIO`|Enable building [libsoundio](http://libsound.io). You must run following command `git clone https://github.com/andrewrk/libsoundio dependencies/libsoundio` before enabling the option.|
|`NANOEM_ENABLE_BUILD_GLFW`|Enable building [GLTF](https://www.glfw.org). You must run following command `git clone https://github.com/glfw/glfw dependencies/libsoundio` before enabling the option.|
|`NANOEM_ENABLE_BUILD_FFMPEG`|Enable building [ffmpeg](http://ffmpeg.org). You must run following command `git clone https://github.com/ffmpeg/ffmpeg dependencies/ffmpeg` before enabling the option.|
|`NANOEM_ENABLE_BUILD_YAMLCPP`|Enable building [yaml-cpp](https://github.com/jbeder/yaml-cpp). You must run following command `git clone https://github.com/jbeder/yaml-cpp dependencies/yaml-cpp` before enabling the option.|

</details>

## Architecture

See [Architecture Document](docs/architecture.rst) (Japanese)

## About License

- nanoem component is licensed under [MIT/X11 License](LICENSE.MIT).
- emapp/win32/macos/glfw/sapp components are licensed under [Mozilla Public License](LICENSE.MPL).

## Contributors

<a href="https://github.com/hkrn/nanoem/graphs/contributors">
  <img src="https://contributors-img.web.app/image?repo=hkrn/nanoem" />
</a>
