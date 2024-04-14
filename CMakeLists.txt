cmake_minimum_required(VERSION 3.15)
project(yaml-cpp LANGUAGES CXX)
set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# Static runtime
set(CMAKE_MSVC_RUNTIME_LIBRARY "MultiThreaded$<$<CONFIG:Debug>:Debug>")
set(CMAKE_MSVC_RUNTIME_LIBRARY "MultiThreaded$<$<CONFIG:Release>:Release>")
set(CMAKE_MSVC_RUNTIME_LIBRARY "MultiThreaded$<$<CONFIG:Distribution>:Distribution>")

# Output directories
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/bin/${CMAKE_BUILD_TYPE}/${PROJECT_NAME}")
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/bin/${CMAKE_BUILD_TYPE}/${PROJECT_NAME}")
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/bin/${CMAKE_BUILD_TYPE}/${PROJECT_NAME}")

# Source files
file(GLOB_RECURSE SRC_FILES 
    "src/*.h" 
    "src/*.cpp"
)
file(GLOB_RECURSE INCLUDE_FILES 
    "include/*.h"
)

# Include directories
include_directories(include)

# Add library
add_library(${PROJECT_NAME} STATIC ${SRC_FILES} ${INCLUDE_FILES})

# Define YAML_CPP_STATIC_DEFINE
target_compile_definitions(${PROJECT_NAME} PRIVATE YAML_CPP_STATIC_DEFINE)

# Windows-specific settings
if(WIN32)
    add_compile_options(/W4) # Or the warning level you prefer
    target_compile_definitions(${PROJECT_NAME} PRIVATE _WIN32_WINNT=0x0601) # Windows 7 or later
endif()

# Linux-specific settings
if(UNIX AND NOT APPLE)
    add_compile_options(-Wall -Wextra -pedantic)
endif()

# Debug configuration
if(CMAKE_BUILD_TYPE MATCHES Debug)
    target_compile_options(${PROJECT_NAME} PRIVATE -g)
endif()

# Release configuration
if(CMAKE_BUILD_TYPE MATCHES Release)
    target_compile_options(${PROJECT_NAME} PRIVATE -O3)
endif()

# Distribution configuration
if(CMAKE_BUILD_TYPE MATCHES Distribution)
    target_compile_options(${PROJECT_NAME} PRIVATE -O3)
    target_compile_definitions(${PROJECT_NAME} PRIVATE NDEBUG)
endif()
