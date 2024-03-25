cmake_minimum_required(VERSION 3.10)

# Project configuration
project(yaml-cpp LANGUAGES CXX)

# Set static runtime
set(CMAKE_MSVC_RUNTIME_LIBRARY "MultiThreaded$<$<CONFIG:Debug>:Debug>")

# Output directories
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/bin/${CMAKE_BUILD_TYPE}/yaml-cpp")
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/bin/${CMAKE_BUILD_TYPE}/yaml-cpp")
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/bin/${CMAKE_BUILD_TYPE}/yaml-cpp")

# Files
file(GLOB_RECURSE YAML_CPP_SOURCE_FILES 
    "src/*.cpp" 
    "src/*.h" 
    "include/*.h"
)

# Define the target
add_library(yaml-cpp STATIC ${YAML_CPP_SOURCE_FILES})

# Include directories
target_include_directories(yaml-cpp PRIVATE 
    "include"
)

# Define defines
target_compile_definitions(yaml-cpp PRIVATE YAML_CPP_STATIC_DEFINE)

# System-specific settings
if(WIN32)
    target_compile_definitions(yaml-cpp PRIVATE CRISTAL_PLATFORM_WINDOWS)
    target_compile_options(yaml-cpp PRIVATE /W3)
endif()

if(UNIX)
    target_compile_options(yaml-cpp PRIVATE -Wall -Wextra -pedantic)
endif()

# Configuration-specific settings
if(CMAKE_BUILD_TYPE STREQUAL "Debug")
    target_compile_definitions(yaml-cpp PRIVATE CRISTAL_DEBUG)
    target_compile_options(yaml-cpp PRIVATE $<$<CONFIG:Debug>:/MTd>)
endif()

if(CMAKE_BUILD_TYPE STREQUAL "Release")
    target_compile_definitions(yaml-cpp PRIVATE CRISTAL_RELEASE)
    target_compile_options(yaml-cpp PRIVATE $<$<CONFIG:Release>:/MT>)
endif()

if(CMAKE_BUILD_TYPE STREQUAL "Distribution")
    target_compile_definitions(yaml-cpp PRIVATE CRISTAL_DISTRIBUTION)
    target_compile_options(yaml-cpp PRIVATE $<$<CONFIG:Distribution>:/MT>)
endif()