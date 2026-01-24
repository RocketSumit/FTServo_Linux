# Installation Guide

This guide provides detailed instructions for building and installing FTServo Linux library.

## Quick Start

```bash
# 1. Configure
mkdir build && cd build
cmake ..

# 2. Build
make

# 3. Install (requires sudo)
sudo make install
```

## Prerequisites

- CMake 3.10 or higher
- C++11 compatible compiler (GCC, Clang, etc.)
- Make or Ninja build system

## Detailed Installation Steps

### Step 1: Configure the Build

Navigate to the source directory and create a build directory:

```bash
cd FTServo_Linux
mkdir build
cd build
```

Run CMake to configure the project:

```bash
cmake ..
```

### Step 2: Build Options

#### Custom Installation Path

To install to a different location (e.g., `/usr` instead of `/usr/local`):

```bash
cmake .. -DCMAKE_INSTALL_PREFIX=/usr
```

#### Build Shared Library

By default, a static library is built. To build a shared library:

```bash
cmake .. -DBUILD_SHARED_LIBS=ON
```

**Note:** See [README.md](README.md) for differences between static and shared libraries.

#### Combined Options

```bash
cmake .. -DCMAKE_INSTALL_PREFIX=/usr -DBUILD_SHARED_LIBS=ON
```

**Note:** Examples are not built by default. Build them separately from the `examples/` directory after installation. See [README.md](README.md) for instructions.

### Step 3: Build

Compile the library:

```bash
make
```

Or using CMake directly:

```bash
cmake --build .
```

### Step 4: Install

Install system-wide (requires root privileges):

```bash
sudo make install
```

Or using CMake:

```bash
sudo cmake --build . --target install
```

### What Gets Installed

- **Headers**: `${PREFIX}/include/ftservo/*.h`
- **Library**: `${PREFIX}/lib/libSCServo.a` (or `.so` if shared)
- **CMake config**: `${PREFIX}/lib/cmake/FTServo/`
- **pkg-config**: `${PREFIX}/lib/pkgconfig/ftservo.pc`

Where `${PREFIX}` is `/usr/local` by default, or your specified `CMAKE_INSTALL_PREFIX`.

### Step 5: Update Library Cache (Shared Libraries Only)

After installing a shared library to a non-standard location, update the system library cache:

```bash
sudo ldconfig
```

For standard locations (`/usr/lib`, `/usr/local/lib`), this is usually automatic.

## Verification

Verify that installation was successful:

```bash
# Check library
ls /usr/local/lib/libSCServo.*

# Check headers
ls /usr/local/include/ftservo/

# Check pkg-config
pkg-config --modversion ftservo
pkg-config --cflags --libs ftservo
```

## Using the Installed Library

### With CMake

```cmake
cmake_minimum_required(VERSION 3.10)
project(MyProject)

find_package(FTServo REQUIRED)

add_executable(my_app main.cpp)
target_link_libraries(my_app FTServo::SCServo)
```

### With pkg-config

```bash
# Compile directly
g++ -o my_app main.cpp $(pkg-config --cflags --libs ftservo)

# Or in Makefile
CFLAGS += $(shell pkg-config --cflags ftservo)
LDFLAGS += $(shell pkg-config --libs ftservo)
```

### Manual Linking

```bash
g++ -o my_app main.cpp -I/usr/local/include/ftservo -L/usr/local/lib -lSCServo
```

## Troubleshooting

### CMake Can't Find the Package

If `find_package(FTServo)` fails, set the CMake prefix path:

**In CMakeLists.txt:**
```cmake
set(CMAKE_PREFIX_PATH "/usr/local")
find_package(FTServo REQUIRED)
```

**Or when running cmake:**
```bash
cmake .. -DCMAKE_PREFIX_PATH=/usr/local
```

### Library Not Found at Runtime (Shared Libraries)

If you built a shared library and get "library not found" errors at runtime:

1. **Check if library is in a standard location:**
   ```bash
   ls /usr/lib/libSCServo.so
   ls /usr/local/lib/libSCServo.so
   ```

2. **If installed to custom location, add to `LD_LIBRARY_PATH`:**
   ```bash
   export LD_LIBRARY_PATH=/custom/path/lib:$LD_LIBRARY_PATH
   ```

3. **Or update `/etc/ld.so.conf` and run `ldconfig`:**
   ```bash
   echo "/custom/path/lib" | sudo tee -a /etc/ld.so.conf
   sudo ldconfig
   ```

### Permission Denied

Make sure you use `sudo` for the install step:

```bash
sudo make install
```

### Build Errors

- **CMake version too old:** Ensure CMake 3.10 or higher is installed
- **Compiler not found:** Install a C++11 compatible compiler (GCC, Clang)
- **Missing dependencies:** Check that all prerequisites are installed

### Verification Fails

- **Library not found:** Check installation path matches your `CMAKE_INSTALL_PREFIX`
- **Headers not found:** Verify headers are in `${PREFIX}/include/ftservo/`
- **pkg-config errors:** Ensure pkg-config is installed: `sudo apt-get install pkg-config`

## Uninstallation

To remove the installed files:

```bash
sudo rm -rf /usr/local/include/ftservo
sudo rm -rf /usr/local/lib/libSCServo.*
sudo rm -rf /usr/local/lib/cmake/FTServo
sudo rm -f /usr/local/lib/pkgconfig/ftservo.pc
```

If you installed to a custom prefix, replace `/usr/local` with your installation prefix.

After uninstalling a shared library, update the library cache:

```bash
sudo ldconfig
```
