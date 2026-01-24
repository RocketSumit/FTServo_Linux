cmake_minimum_required(VERSION 3.10)
project(FTServo VERSION 1.0.0 LANGUAGES CXX)

# Set C++ standard
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# Build options
option(BUILD_SHARED_LIBS "Build shared library instead of static" OFF)

# Set default install paths
if(CMAKE_INSTALL_PREFIX_INITIALIZED_TO_DEFAULT)
    set(CMAKE_INSTALL_PREFIX "/usr/local" CACHE PATH "Install path prefix" FORCE)
endif()

# Add subdirectories
add_subdirectory(src)

# Installation summary
message(STATUS "")
message(STATUS "FTServo configuration:")
message(STATUS "  Version: ${PROJECT_VERSION}")
message(STATUS "  Build type: ${CMAKE_BUILD_TYPE}")
message(STATUS "  Build shared libraries: ${BUILD_SHARED_LIBS}")
message(STATUS "  Install prefix: ${CMAKE_INSTALL_PREFIX}")
message(STATUS "")
message(STATUS "Note: Examples are not built by default.")
message(STATUS "      Build them separately from the examples/ directory after installation.")
message(STATUS "")
message(STATUS "To install system-wide, run:")
message(STATUS "  sudo cmake --build . --target install")
message(STATUS "")
