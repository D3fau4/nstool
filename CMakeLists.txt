cmake_minimum_required(VERSION 3.22)
project(nstools C CXX ASM)

set(CMAKE_C_STANDARD 11)
set(CMAKE_CXX_STANDARD 11)

MESSAGE(STATUS "Using toolchain file: ${CMAKE_TOOLCHAIN_FILE}")

# Añadir flags de compilación
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -Wall -Wno-unused-value -Wno-unused-but-set-variable -fexceptions")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -Wno-unused-value -Wno-unused-but-set-variable -fexceptions")

add_compile_definitions(PROJECT_PLATFORM "GNU")
# We have to add the cmake directory to have access to
# the Switch tools and also find_package for libnx.
list(APPEND CMAKE_MODULE_PATH ${CMAKE_SOURCE_DIR}/cmake)

# SwitchTools provide various helper functions for building Switch file formats.
include(SwitchTools)

set(LIBS_DIR deps/)
set(FMT_DIR ${LIBS_DIR}/libfmt)
set(LZ4_DIR ${LIBS_DIR}/liblz4)
set(MBEDTLS_DIR ${LIBS_DIR}/libmbedtls)
set(PIENTENDO_DIR ${LIBS_DIR}/libpietendo)
set(TOOLCHAIN_DIR ${LIBS_DIR}/libtoolchain)

file(GLOB FMT_SRC
        ${FMT_DIR}/src/*.cc
)

file(GLOB LZ4_SRC
        ${LZ4_DIR}/src/*.c
)

file(GLOB MBEDTLS_SRC
        ${MBEDTLS_DIR}/src/*.c
)

file(GLOB PIENTENDO_SRC
        ${PIENTENDO_DIR}/src/**/*.cpp
)

file(GLOB TOOLCHAIN_SRC
        ${TOOLCHAIN_DIR}/src/**/*.cpp
        ${TOOLCHAIN_DIR}/src/*.cpp
)

file(GLOB NSTOOL_SRC
        src/*.cpp
)

# Define the target before setting properties
add_library(nstools ${NSTOOL_SRC} ${FMT_SRC} ${LZ4_SRC} ${PIENTENDO_SRC} ${TOOLCHAIN_SRC})

# Now you can specify include directories and link libraries for the target
target_include_directories(nstools PUBLIC
        $<BUILD_INTERFACE:${CMAKE_SOURCE_DIR}/include>
        ${FMT_DIR}/include
        ${LZ4_DIR}/include
        ${MBEDTLS_DIR}/include
        ${PIENTENDO_DIR}/include
        ${TOOLCHAIN_DIR}/include
)

# Portlibs and libnx static libraries
include_directories("${PORTLIBS}/include")

target_link_libraries(nstools
        -L${PORTLIBS}/lib
)
