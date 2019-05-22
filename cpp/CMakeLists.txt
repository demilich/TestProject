cmake_minimum_required(VERSION 3.14)
message(STATUS "Building using CMake version: ${CMAKE_VERSION}")

set(TESTPROJECT_VERSION "0.1.0")

string(REGEX MATCH "^[0-9]+\\.[0-9]+\\.[0-9]+" TESTPROJECT_BASE_VERSION "${TESTPROJECT_VERSION}")

project(testproject VERSION "${TESTPROJECT_BASE_VERSION}")
project(vecwise_engine LANGUAGES CXX)

set(TESTPROJECT_VERSION_MAJOR "${testproject_VERSION_MAJOR}")
set(TESTPROJECT_VERSION_MINOR "${testproject_VERSION_MINOR}")
set(TESTPROJECT_VERSION_PATCH "${testproject_VERSION_PATCH}")

if(TESTPROJECT_VERSION_MAJOR STREQUAL ""
        OR TESTPROJECT_VERSION_MINOR STREQUAL ""
        OR TESTPROJECT_VERSION_PATCH STREQUAL "")
    message(FATAL_ERROR "Failed to determine TestProject version from '${TESTPROJECT_VERSION}'")
endif()

message(STATUS "TestProject version: "
        "${TESTPROJECT_VERSION_MAJOR}.${TESTPROJECT_VERSION_MINOR}.${TESTPROJECT_VERSION_PATCH} "
        "(full: '${TESTPROJECT_VERSION}')")

set(TESTPROJECT_SOURCE_DIR ${PROJECT_SOURCE_DIR})
set(TESTPROJECT_BINARY_DIR ${PROJECT_BINARY_DIR})

set(CMAKE_CXX_STANDARD 14)

if(CMAKE_SYSTEM_PROCESSOR MATCHES "(x86)|(X86)|(amd64)|(AMD64)")
    message("building vecwise_engine on x86 architecture")
    set(VECWISE_BUILD_ARCH x86_64)
elseif(CMAKE_SYSTEM_PROCESSOR MATCHES "(ppc)")
    message("building vecwise_engine on ppc architecture")
    set(VECWISE_BUILD_ARCH ppc64le)
else()
    message("unknown processor type")
    message("CMAKE_SYSTEM_PROCESSOR=${CMAKE_SYSTEM_PROCESSOR}")
    set(VECWISE_BUILD_ARCH unknown)
endif()

set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_CURRENT_SOURCE_DIR}/cmake")

option(TESTPROJECT_BUILD_TESTS "Build the testproject test suite" ON)


include(ExternalProject)
include(ThirdPartyPackages)

if(CMAKE_BUILD_TYPE STREQUAL "Release")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -O3 -fPIC -DELPP_THREAD_SAFE")
else()
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -O0 -g -fPIC -DELPP_THREAD_SAFE")
endif()

include_directories(${TESTPROJECT_SOURCE_DIR})
link_directories(${TESTPROJECT_BINARY_DIR})

add_subdirectory(src)

add_custom_target(Clean-All COMMAND ${CMAKE_BUILD_TOOL} clean)