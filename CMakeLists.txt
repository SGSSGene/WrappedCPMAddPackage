# SPDX-FileCopyrightText: 2024 Simon Gene Gottlieb
# SPDX-License-Identifier: CC0-1.0

cmake_minimum_required(VERSION 3.14 FATAL_ERROR)

include(cmake/CPM.cmake)
function(WrappedCPMAddPackage arg)

    if (arg MATCHES "^gh::google/googletest")
        set(BUILD_GMOCK OFF)
        CPMAddPackage("${arg}")
    elseif (arg MATCHES "^gh:google/benchmark")
        set(BENCHMARK_ENABLE_TESTING OFF)
        CPMAddPackage("${arg}")
    elseif (arg MATCHES "^gh:boost-ext/reflect")
        CPMAddPackage("${arg}")
        add_library(reflect INTERFACE)
        target_include_directories(reflect SYSTEM INTERFACE ${reflect_SOURCE_DIR})
        add_library(reflect::reflect ALIAS reflect)
    elseif (arg MATCHES "^gh:simd-everywhere/simde")
        CPMAddPackage("${arg}")
        add_library(simde INTERFACE)
        target_include_directories(simde SYSTEM INTERFACE ${simde_SOURCE_DIR})
        add_library(simde::simde ALIAS simde)
    elseif (arg MATCHES "^https://zlib.net/zlib-.*.tar.gz")
        CPMAddPackage("${arg}")
        add_library(zlib::zlib ALIAS zlib)
    elseif (arg MATCHES "^gh:facebook/zstd")
        CPMAddPackage("${arg}")
        add_subdirectory(${zstd_SOURCE_DIR}/build/cmake zstd)
        add_library(zstd::zstd ALIAS libzstd_static)
    elseif (arg MATCHES "^https://sourceware.org/pub/bzip2/bzip2-.*tar.gz")
        CPMAddPackage("${arg}")
        add_library(bzip2
            ${bzip2_SOURCE_DIR}/blocksort.c
            ${bzip2_SOURCE_DIR}/bzip2.c
            ${bzip2_SOURCE_DIR}/bzlib.c
            ${bzip2_SOURCE_DIR}/compress.c
            ${bzip2_SOURCE_DIR}/crctable.c
            ${bzip2_SOURCE_DIR}/decompress.c
            ${bzip2_SOURCE_DIR}/dlltest.c
            ${bzip2_SOURCE_DIR}/huffman.c
            ${bzip2_SOURCE_DIR}/makefile.msc
            ${bzip2_SOURCE_DIR}/mk251.c
            ${bzip2_SOURCE_DIR}/randtable.c
            ${bzip2_SOURCE_DIR}/spewG.c
        )
        target_include_directories(bzip2 SYSTEM INTERFACE ${bzip2_SOURCE_DIR})

        add_library(bzip2::bzip2 ALIAS bzip2)
    endif()

endfunction()
