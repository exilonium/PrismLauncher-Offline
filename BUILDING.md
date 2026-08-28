# Building Prism Launcher (Offline)

This guide covers how to build Prism Launcher (Offline) from source.

## Prerequisites

All platforms require:

- **CMake** 3.25 or newer
- **Qt 6.4** or newer (Core, Widgets, Concurrent, Network, Xml, NetworkAuth, OpenGL)
- **Java Development Kit** (for building the Java-based launcher component)
- **Git**

### Linux

Install the following dependencies (package names may vary by distro):

On **Arch Linux**:

```sh
sudo pacman -S cmake ninja qt6-base extra-cmake-modules cmark libqrencode libarchive tomlplusplus zlib gamemode
```

On **Ubuntu/Debian** (Ubuntu 24.04+):

```sh
sudo apt install cmake ninja-build qt6-base-dev qt6-base-private-dev libqt6networkauth6-dev extra-cmake-modules libcmark-dev libgamemode-dev libqrencode-dev libarchive-dev libtomlplusplus-dev zlib1g-dev
```

On **Fedora**:

```sh
sudo dnf install cmake ninja-build qt6-qtbase-devel extra-cmake-modules cmark-devel gamemode-devel qrencode-devel libarchive-devel tomlplusplus-devel zlib-devel
```

### macOS

Install [Xcode](https://developer.apple.com/xcode/) and [vcpkg](https://vcpkg.io/), then:

```sh
brew install cmake ninja
```

Set the `VCPKG_ROOT` environment variable to your vcpkg installation directory.

### Windows

- [Visual Studio 2022](https://visualstudio.microsoft.com/) (with C++ workload) **or** [MinGW-w64](https://www.mingw-w64.org/)
- [CMake](https://cmake.org/download/)
- [Ninja](https://ninja-build.org/)
- [vcpkg](https://vcpkg.io/) (required for MSVC builds)

Set the `VCPKG_ROOT` environment variable to your vcpkg installation directory for MSVC builds.

---

## Cloning the Repository

```sh
git clone --recurse-submodules https://github.com/exilonium/PrismLauncher-Offline.git
cd PrismLauncher-Offline
```

If you already cloned without submodules:

```sh
git submodule update --init --recursive
```

---

## Building

### Using CMake Presets (recommended)

This project ships with `CMakePresets.json` for common configurations.

#### Linux (CMake Preset)

```sh
cmake --preset linux
cmake --build build --preset linux
```

#### macOS (CMake Preset)

```sh
cmake --preset macos
cmake --build build --preset macos
```

For a Universal Binary (x86\_64 + arm64):

```sh
cmake --preset macos_universal
cmake --build build --preset macos_universal
```

#### Windows (MinGW)

```sh
cmake --preset windows_mingw
cmake --build build --preset windows_mingw
```

#### Windows (MSVC)

```sh
cmake --preset windows_msvc
cmake --build build --preset windows_msvc
```

---

### Manual CMake Configuration

If you prefer to configure manually (or the presets don't fit your setup):

```sh
mkdir build
cd build
cmake .. -G "Ninja" -DCMAKE_BUILD_TYPE=Release
cmake --build . --parallel
```

#### Useful CMake Options

| Option | Default | Description |
| --- | --- | --- |
| `Launcher_QT_VERSION_MAJOR` | `6` | Major Qt version to build against |
| `Launcher_ENABLE_JAVA_DOWNLOADER` | `ON` (Win/macOS), `OFF` (Linux) | Build the Java downloader feature |
| `Launcher_USE_PCH` | `ON` | Use precompiled headers |
| `ENABLE_LTO` | `OFF` | Enable Link Time Optimization |
| `BUILD_TESTING` | `ON` | Build the test suite |
| `DEBUG_ADDRESS_SANITIZER` | `OFF` | Enable AddressSanitizer in debug builds |
| `USE_CLANG_TIDY` | `OFF` | Run clang-tidy during compilation |

---

## Installing

```sh
cmake --install build --prefix /usr/local
```

For a portable installation (Linux/Windows):

```sh
cmake --install build --prefix /path/to/install --component portable
```

---

## Running Tests

```sh
ctest --preset linux          # Linux
ctest --preset macos          # macOS
ctest --preset windows_mingw  # Windows (MinGW)
ctest --preset windows_msvc   # Windows (MSVC)
```

Or manually from the build directory:

```sh
cd build
ctest --output-on-failure
```

---

## Notes

- **In-source builds are not supported.** Always use a separate build directory.
- This fork does **not** enforce Microsoft login. The Microsoft authentication flow is present but optional; users can play in offline mode without signing in to a Microsoft account.
- The `Launcher_MSA_CLIENT_ID` CMake variable controls the Microsoft OAuth client ID. You may set your own if you are rebranding or building a derivative.

---

## Troubleshooting

- **Qt not found**: Make sure `Qt6_DIR` or `CMAKE_PREFIX_PATH` points to your Qt 6 installation.
- **ECM (Extra CMake Modules) not found**: Install `extra-cmake-modules` from your package manager.
- **vcpkg issues on Windows/macOS**: Ensure `VCPKG_ROOT` is set and you have run `vcpkg install` inside the repo (vcpkg manifest mode is used).
- **Submodule errors**: Run `git submodule update --init --recursive` to ensure all submodules are checked out.
