# Source files
file(GLOB hdrs *.h)
file(GLOB srcs *.cpp)

# Create library (static or shared based on BUILD_SHARED_LIBS)
add_library(SCServo ${srcs})

# Set target properties
target_include_directories(SCServo
    PUBLIC
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
        $<INSTALL_INTERFACE:include/ftservo>
)

# Set C++ standard and optimization
target_compile_features(SCServo PUBLIC cxx_std_11)
target_compile_options(SCServo PRIVATE -O3)

# Add -fPIC for static libraries to allow linking into shared libraries
if(NOT BUILD_SHARED_LIBS)
    set_target_properties(SCServo PROPERTIES POSITION_INDEPENDENT_CODE ON)
endif()

# Alias target for consistency
add_library(FTServo::SCServo ALIAS SCServo)

# Installation
# Install headers
install(FILES ${hdrs}
    DESTINATION include/ftservo
    COMPONENT Development
)

# Install library
install(TARGETS SCServo
    EXPORT FTServoTargets
    LIBRARY DESTINATION lib
    ARCHIVE DESTINATION lib
    RUNTIME DESTINATION bin
    COMPONENT Runtime
    INCLUDES DESTINATION include/ftservo
)

# Install CMake package files
include(CMakePackageConfigHelpers)

# Create package version file
write_basic_package_version_file(
    "${CMAKE_CURRENT_BINARY_DIR}/FTServoConfigVersion.cmake"
    VERSION ${PROJECT_VERSION}
    COMPATIBILITY AnyNewerVersion
)

# Create package config file
set(PACKAGE_INCLUDE_INSTALL_DIR "${CMAKE_INSTALL_PREFIX}/include/ftservo")
set(FTServo_VERSION ${PROJECT_VERSION})
configure_package_config_file(
    "${CMAKE_CURRENT_SOURCE_DIR}/../cmake/FTServoConfig.cmake.in"
    "${CMAKE_CURRENT_BINARY_DIR}/FTServoConfig.cmake"
    INSTALL_DESTINATION lib/cmake/FTServo
    PATH_VARS PACKAGE_INCLUDE_INSTALL_DIR FTServo_VERSION
)

# Configure pkg-config file
configure_file(
    "${CMAKE_CURRENT_SOURCE_DIR}/../cmake/ftservo.pc.in"
    "${CMAKE_CURRENT_BINARY_DIR}/ftservo.pc"
    @ONLY
)

# Install CMake package files
install(EXPORT FTServoTargets
    FILE FTServoTargets.cmake
    NAMESPACE FTServo::
    DESTINATION lib/cmake/FTServo
    COMPONENT Development
)

install(FILES
    "${CMAKE_CURRENT_BINARY_DIR}/FTServoConfig.cmake"
    "${CMAKE_CURRENT_BINARY_DIR}/FTServoConfigVersion.cmake"
    DESTINATION lib/cmake/FTServo
    COMPONENT Development
)

# Install pkg-config file for easier integration
install(FILES
    "${CMAKE_CURRENT_BINARY_DIR}/ftservo.pc"
    DESTINATION lib/pkgconfig
    COMPONENT Development
)
