# FTServo Linux

FEETECH BUS Servo Linux library - A system-wide installable servo motor driver package.

> **Note:** This repository is a fork of [FTServo_Linux](https://github.com/ftservo/FTServo_Linux) with improvements to installation, build system, and usage. The original repository provides the core library functionality, while this fork enhances it with:
>
> - Modern CMake build system with system-wide installation support
> - CMake package configuration for easy integration (`find_package(FTServo)`)
> - pkg-config support for non-CMake projects
> - Restructured examples with automatic library detection
> - Comprehensive documentation and installation guides

## Overview

FTServo is a C++ library for controlling FEETECH BUS servos on Linux systems. It supports multiple servo series:

- **HLSCL**: High-level serial communication library
- **SCSCL**: SCSCL series servos
- **SMS_STS**: SMS/STS series servos

## Quick Start

### Prerequisites

- CMake 3.10 or higher
- C++11 compatible compiler (GCC, Clang, etc.)
- Make or Ninja build system

### Installation

```bash
# 1. Configure
mkdir build && cd build
cmake ..

# 2. Build
make

# 3. Install (requires sudo)
sudo make install
```

For detailed installation instructions, build options, and troubleshooting, see [INSTALL.md](INSTALL.md).

## Using the Library

### With CMake (Recommended)

```cmake
cmake_minimum_required(VERSION 3.10)
project(MyProject)

find_package(FTServo REQUIRED)

add_executable(my_app main.cpp)
target_link_libraries(my_app FTServo::SCServo)
```

The library headers will be automatically included via the `FTServo::SCServo` target.

### With pkg-config

```bash
g++ -o my_app main.cpp $(pkg-config --cflags --libs ftservo)
```

### Include Headers

```cpp
#include <ftservo/SCServo.h>  // Main header (includes all servo types)
// Or include specific headers:
#include <ftservo/HLSCL.h>
#include <ftservo/SCSCL.h>
#include <ftservo/SMS_STS.h>
```

## Examples

Example programs are available in the `examples/` directory. **Examples are not built by default** - they should be built separately after installing the library.

### Building Examples After Installation

**Option 1: Build All Examples**

After installing the library, build all examples at once:

```bash
cd examples
mkdir build && cd build
cmake ..
make
```

**Option 2: Build Individual Examples**

Navigate to any example directory and build it:

```bash
cd examples/SMS_STS/WritePos
mkdir build && cd build
cmake ..
make
sudo ./WritePos /dev/ttyUSB0
```

Replace `/dev/ttyUSB0` with your actual serial port device.

**Note:** Examples automatically detect whether the library is installed. If the library is not found, examples will attempt to build it from the source tree (when building from the repository root).

## Build Options

- `BUILD_SHARED_LIBS`: Build shared library instead of static (default: OFF)
- `CMAKE_INSTALL_PREFIX`: Installation prefix (default: `/usr/local`)

**Note:** Examples are not built by default. Build them separately from the `examples/` directory after installation.

See [INSTALL.md](INSTALL.md) for detailed information on build options and configuration.

## Installation Locations

After installation, files are installed to:

- **Headers**: `${PREFIX}/include/ftservo/`
- **Libraries**: `${PREFIX}/lib/`
- **CMake config**: `${PREFIX}/lib/cmake/FTServo/`
- **pkg-config**: `${PREFIX}/lib/pkgconfig/ftservo.pc`

Where `${PREFIX}` defaults to `/usr/local` or your specified `CMAKE_INSTALL_PREFIX`.

## Uninstallation

To remove the installed files:

```bash
sudo rm -rf /usr/local/include/ftservo
sudo rm -rf /usr/local/lib/libSCServo.*
sudo rm -rf /usr/local/lib/cmake/FTServo
sudo rm -f /usr/local/lib/pkgconfig/ftservo.pc
```

## License

See [LICENSE](LICENSE) file for details.

## Support

For issues and questions, please refer to the FEETECH documentation or contact support.
