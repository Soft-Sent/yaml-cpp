cmake_minimum_required(VERSION 3.10)

# Define the project name and language
project(yaml-cpp VERSION 1.0 LANGUAGES CXX)

# Set C++ standard
set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

# Enable static runtime if supported (MSVC specific)
if(MSVC)
    foreach(flag_var
            CMAKE_CXX_FLAGS CMAKE_CXX_FLAGS_DEBUG CMAKE_CXX_FLAGS_RELEASE
            CMAKE_CXX_FLAGS_MINSIZEREL CMAKE_CXX_FLAGS_RELWITHDEBINFO)
        if(${flag_var} MATCHES "/MD")
            string(REGEX REPLACE "/MD" "/MT" ${flag_var} "${${flag_var}}")
        endif()
    endforeach()
endif()

# Set output directories
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin/${CMAKE_BUILD_TYPE})
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib/${CMAKE_BUILD_TYPE})

# Add source files
file(GLOB_RECURSE SOURCES 
    "src/**/*.h"
    "src/**/*.cpp"
    "include/**/*.h"
)

add_library(yaml-cpp STATIC ${SOURCES})

# Include directories
target_include_directories(yaml-cpp PRIVATE
    "${CMAKE_CURRENT_SOURCE_DIR}/include"
)

# Defines
target_compile_definitions(yaml-cpp PRIVATE 
    YAML_CPP_STATIC_DEFINE
)

# Windows specific settings
if(WIN32)
    set_target_properties(yaml-cpp PROPERTIES LINKER_LANGUAGE CXX)
    set_property(TARGET yaml-cpp PROPERTY MSVC_RUNTIME_LIBRARY "MultiThreaded$<$<CONFIG:Debug>:Debug>")
endif()

# Linux specific settings
if(UNIX AND NOT APPLE)
    set_target_properties(yaml-cpp PROPERTIES POSITION_INDEPENDENT_CODE ON)
endif()

# Configuration-specific settings
foreach(CONFIG IN ITEMS Debug Release Distribution)
    string(TOUPPER ${CONFIG} CONFIG_UPPER)
    if(CONFIG STREQUAL "Debug")
        set_target_properties(yaml-cpp PROPERTIES 
            COMPILE_DEFINITIONS_${CONFIG_UPPER} ""
            COMPILE_OPTIONS "$<$<CONFIG:${CONFIG}>:/DEBUG>"
            LINK_FLAGS "$<$<CONFIG:${CONFIG}>:/DEBUG>"
        )
    elseif(CONFIG STREQUAL "Release")
        set_target_properties(yaml-cpp PROPERTIES 
            COMPILE_DEFINITIONS_${CONFIG_UPPER} ""
            COMPILE_OPTIONS "$<$<CONFIG:${CONFIG}>:/OPT:REF>"
            LINK_FLAGS "$<$<CONFIG:${CONFIG}>:/OPT:REF>"
        )
    elseif(CONFIG STREQUAL "Distribution")
        set_target_properties(yaml-cpp PROPERTIES 
            COMPILE_DEFINITIONS_${CONFIG_UPPER} ""
            COMPILE_OPTIONS "$<$<CONFIG:${CONFIG}>:/OPT:REF>"
            LINK_FLAGS "$<$<CONFIG:${CONFIG}>:/OPT:REF>"
        )
    endif()
endforeach()