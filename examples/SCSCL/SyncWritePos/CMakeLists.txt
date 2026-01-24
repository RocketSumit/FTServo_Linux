cmake_minimum_required(VERSION 3.10)

# Get the example name from directory
get_filename_component(EXAMPLE_NAME ${CMAKE_CURRENT_SOURCE_DIR} NAME)

project(${EXAMPLE_NAME})

# Try to find installed FTServo package
find_package(FTServo QUIET)

# Get source files
file(GLOB SOURCES "*.cpp")
file(GLOB HEADERS "*.h")

if(SOURCES)
    add_executable(${EXAMPLE_NAME} ${SOURCES} ${HEADERS})
    
    if(FTServo_FOUND)
        # Use installed library
        target_link_libraries(${EXAMPLE_NAME} FTServo::SCServo)
        message(STATUS "Example ${EXAMPLE_NAME}: Using installed FTServo library")
    else()
        # Fallback to local build (when building from source tree)
        if(EXISTS "${CMAKE_CURRENT_SOURCE_DIR}/../../../../src/CMakeLists.txt")
            # Link to local build
            add_subdirectory(../../../../src ${CMAKE_BINARY_DIR}/ftservo_src)
            target_link_libraries(${EXAMPLE_NAME} SCServo)
            # Create compatibility include for <ftservo/SCServo.h>
            file(MAKE_DIRECTORY ${CMAKE_BINARY_DIR}/ftservo_include)
            execute_process(
                COMMAND ${CMAKE_COMMAND} -E create_symlink 
                ${CMAKE_CURRENT_SOURCE_DIR}/../../../../src 
                ${CMAKE_BINARY_DIR}/ftservo_include/ftservo
                RESULT_VARIABLE symlink_result
                ERROR_QUIET
            )
            target_include_directories(${EXAMPLE_NAME} PRIVATE ${CMAKE_BINARY_DIR}/ftservo_include)
            message(STATUS "Example ${EXAMPLE_NAME}: Using local FTServo build")
        else()
            message(FATAL_ERROR "FTServo library not found. Please install it first or build from source tree.")
        endif()
    endif()
    
    # Set C++ standard
    set_target_properties(${EXAMPLE_NAME} PROPERTIES
        CXX_STANDARD 11
        CXX_STANDARD_REQUIRED ON
    )
    
    # Set optimization
    target_compile_options(${EXAMPLE_NAME} PRIVATE -O3)
else()
    message(FATAL_ERROR "No source files found in ${CMAKE_CURRENT_SOURCE_DIR}")
endif()
