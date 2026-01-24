cmake_minimum_required(VERSION 3.10)

# This CMakeLists.txt builds all example programs
# Examples can be built individually from their directories, or all at once from here

# Try to find installed FTServo package first
find_package(FTServo QUIET)

# If not found, use local build (when building from source tree)
if(NOT FTServo_FOUND)
    # Check if we're in the source tree
    if(EXISTS "${CMAKE_CURRENT_SOURCE_DIR}/../../src/CMakeLists.txt")
        # Add the source directory to build the library locally
        add_subdirectory(../../src ${CMAKE_BINARY_DIR}/ftservo_src)
        set(FTSERVO_USE_LOCAL_BUILD TRUE)
        message(STATUS "Using local FTServo build from source tree")
        # Create compatibility include directory for <ftservo/SCServo.h>
        file(MAKE_DIRECTORY ${CMAKE_BINARY_DIR}/ftservo_include)
    else()
        message(FATAL_ERROR "FTServo library not found. Please install it first or build from source tree.")
    endif()
endif()

# Helper function to add an example
function(add_ftservo_example example_name example_dir)
    # Get source files
    file(GLOB example_sources "${example_dir}/*.cpp")
    file(GLOB example_headers "${example_dir}/*.h")
    
    if(example_sources)
        add_executable(${example_name} ${example_sources} ${example_headers})
        
        # Link to FTServo library
        if(FTSERVO_USE_LOCAL_BUILD)
            target_link_libraries(${example_name} SCServo)
            # For local builds, create symlink so <ftservo/SCServo.h> works
            # The ftservo_include directory was created above
            execute_process(
                COMMAND ${CMAKE_COMMAND} -E create_symlink 
                ${CMAKE_CURRENT_SOURCE_DIR}/../../src 
                ${CMAKE_BINARY_DIR}/ftservo_include/ftservo
                RESULT_VARIABLE symlink_result
                ERROR_QUIET
            )
            target_include_directories(${example_name} PRIVATE ${CMAKE_BINARY_DIR}/ftservo_include)
        else()
            target_link_libraries(${example_name} FTServo::SCServo)
        endif()
        
        # Set C++ standard
        set_target_properties(${example_name} PROPERTIES
            CXX_STANDARD 11
            CXX_STANDARD_REQUIRED ON
        )
        
        # Set optimization
        target_compile_options(${example_name} PRIVATE -O3)
        
        message(STATUS "Added example: ${example_name}")
    else()
        message(WARNING "No source files found for example: ${example_name} in ${example_dir}")
    endif()
endfunction()

# Build HLSCL examples
add_ftservo_example(HLSCL_Broadcast HLSCL/Broadcast)
add_ftservo_example(HLSCL_CalibrationOfs HLSCL/CalibrationOfs)
add_ftservo_example(HLSCL_FeedBack HLSCL/FeedBack)
add_ftservo_example(HLSCL_Ping HLSCL/Ping)
add_ftservo_example(HLSCL_ProgramEprom HLSCL/ProgramEprom)
add_ftservo_example(HLSCL_RegWritePos HLSCL/RegWritePos)
add_ftservo_example(HLSCL_SyncRead HLSCL/SyncRead)
add_ftservo_example(HLSCL_SyncWritePos HLSCL/SyncWritePos)
add_ftservo_example(HLSCL_WritePos HLSCL/WritePos)
add_ftservo_example(HLSCL_WriteSpe HLSCL/WriteSpe)

# Build SCSCL examples
add_ftservo_example(SCSCL_Broadcast SCSCL/Broadcast)
add_ftservo_example(SCSCL_FeedBack SCSCL/FeedBack)
add_ftservo_example(SCSCL_Ping SCSCL/Ping)
add_ftservo_example(SCSCL_ProgramEprom SCSCL/ProgramEprom)
add_ftservo_example(SCSCL_RegWritePos SCSCL/RegWritePos)
add_ftservo_example(SCSCL_SyncWritePos SCSCL/SyncWritePos)
add_ftservo_example(SCSCL_WritePos SCSCL/WritePos)
add_ftservo_example(SCSCL_WritePWM SCSCL/WritePWM)

# Build SMS_STS examples
add_ftservo_example(SMS_STS_Broadcast SMS_STS/Broadcast)
add_ftservo_example(SMS_STS_CalibrationOfs SMS_STS/CalibrationOfs)
add_ftservo_example(SMS_STS_FeedBack SMS_STS/FeedBack)
add_ftservo_example(SMS_STS_Ping SMS_STS/Ping)
add_ftservo_example(SMS_STS_ProgramEprom SMS_STS/ProgramEprom)
add_ftservo_example(SMS_STS_RegWritePos SMS_STS/RegWritePos)
add_ftservo_example(SMS_STS_SyncRead SMS_STS/SyncRead)
add_ftservo_example(SMS_STS_SyncWritePos SMS_STS/SyncWritePos)
add_ftservo_example(SMS_STS_WritePos SMS_STS/WritePos)
add_ftservo_example(SMS_STS_WriteSpe SMS_STS/WriteSpe)

message(STATUS "")
message(STATUS "Examples configuration complete!")
message(STATUS "Build all examples with: cmake --build .")
message(STATUS "")
