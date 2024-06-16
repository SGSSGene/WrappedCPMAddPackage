# SPDX-FileCopyrightText: 2024 Simon Gene Gottlieb
# SPDX-License-Identifier: CC0-1.0

cmake_minimum_required(VERSION 3.14 FATAL_ERROR)

include(cmake/CPM.cmake)
function(WrappedCPMAddPackage arg)

    if (arg MATCHES "^gh:ChunelFeng/CGraph")
        CPMAddPackage("${arg}" ${ARGN})
        add_library(cgraph STATIC $<TARGET_OBJECTS:CGraph>)
        target_link_libraries(cgraph INTERFACE CGraph)
        target_include_directories(cgraph SYSTEM INTERFACE ${CGraph_SOURCE_DIR}/src)
        add_library(cgraph::cgraph ALIAS cgraph)
    elseif (arg MATCHES "^gh:USCiLab/cereal")
        set(BUILD_DOC OFF)
        set(BUILD_SANDBOX OFF)
        set(SKIP_PERFORMANCE_COMPARISON ON)
        CPMAddPackage("${arg}" ${ARGN})
    elseif (arg MATCHES "^gh:google/benchmark")
        set(BENCHMARK_ENABLE_TESTING OFF)
        set(BENCHMARK_ENABLE_INSTALL OFF)
        set(BENCHMARK_ENABLE_GTEST_TESTS OFF)
        CPMAddPackage("${arg}" ${ARGN})
    elseif (arg MATCHES "^gh:google/googletest")
        set(BUILD_GMOCK OFF)
        CPMAddPackage("${arg}" ${ARGN})
    elseif (arg MATCHES "^gh:boost-ext/reflect")
        CPMAddPackage("${arg}" ${ARGN})
        add_library(reflect INTERFACE)
        target_include_directories(reflect SYSTEM INTERFACE ${reflect_SOURCE_DIR})
        target_compile_options(reflect INTERFACE "-std=c++20")
        add_library(reflect::reflect ALIAS reflect)
    elseif (arg MATCHES "^gh:simd-everywhere/simde")
        CPMAddPackage("${arg}" ${ARGN})
        macro (wrapped_cpm_define_simde SIMDE_INCLUDE_DIRECTORY)
            add_library (simde INTERFACE)
            add_library (simde::simde ALIAS simde)
            target_include_directories (simde INTERFACE "$<BUILD_INTERFACE:${SIMDE_INCLUDE_DIRECTORY}>")
            set_target_properties (simde PROPERTIES INTERFACE_SYSTEM_INCLUDE_DIRECTORIES
                                                    $<TARGET_PROPERTY:simde,INTERFACE_INCLUDE_DIRECTORIES>)
        endmacro ()

        if (CPM_USE_LOCAL_PACKAGES)
            find_package (PkgConfig QUIET)
            if (PKG_CONFIG_FOUND)
                pkg_check_modules (simde QUIET simde>=${HIBF_SIMDE_VERSION})
            endif ()
        endif ()

        if (simde_FOUND)
            wrapped_cpm_define_simde ("${simde_INCLUDEDIR}")
            cpm_message (STATUS "${CPM_INDENT} Using local package simde@${simde_VERSION}")
        else ()
            wrapped_cpm_define_simde ("${simde_SOURCE_DIR}")
        endif ()
    elseif (arg MATCHES "^https://zlib.net/zlib-.*.tar.gz")
        set(ZLIB_BUILD_EXAMPLES OFF)
        CPMAddPackage("${arg}" ${ARGN})
        add_library(zlib::zlib ALIAS zlib)
    elseif (arg MATCHES "^gh:facebook/zstd")
        set(ZSTD_BUILD_PROGRAMS FALSE)
        set(ZSTD_BUILD_TESTS FALSE)
        CPMAddPackage("${arg}" ${ARGN})
        add_subdirectory(${zstd_SOURCE_DIR}/build/cmake zstd)
        add_library(zstd::zstd ALIAS libzstd_static)
    elseif (arg MATCHES "^https://sourceware.org/pub/bzip2/bzip2-.*tar.gz")
        CPMAddPackage("${arg}" ${ARGN})
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
    elseif (arg MATCHES "^gh:glfw/glfw")
        set(GLFW_BUILD_TESTS OFF)
        set(GLFW_BUILD_EXAMPLES OFF)
        set(GLFW_BUILD_DOCS OFF)
        set(GLFW_INSTALL OFF)
        set(GLFW_USE_HYBRID_HPG ON)
        CPMAddPackage("${arg}" ${ARGN})
    elseif (arg MATCHES "^gh:google/flatbuffers")
        set(FLATBUFFERS_BUILD_TESTS OFF)
        set(FLATBUFFERS_INSTALL OFF)
        CPMAddPackage("${arg}" ${ARGN})
    elseif (arg MATCHES "^gh:microsoft/mimalloc")
        set(MI_BUILD_TESTS OFF)
        CPMAddPackage("${arg}" ${ARGN})
    elseif (arg MATCHES "^gh:libsndfile/libsamplerate")
        set(BUILD_TESTING_BAK ${BUILD_TESTING})
        set(BUILD_TESTING OFF)
        set(LIBSAMPLERATE_EXAMPLES OFF)
        set(LIBSAMPLERATE_INSTALL OFF)
        set(LIBSAMPLERATE_INSTALL_PKGCONFIG OFF)
        CPMAddPackage("${arg}" ${ARGN})
        set(BUILD_TESTING ${BUILD_TESTING_BAK})
    elseif (arg MATCHES "^gh:libsndfile/libsndfile")
        set(BUILD_TESTING_BAK ${BUILD_TESTING})
        set(BUILD_TESTING OFF)
        set(BUILD_PROGRAMS OFF)
        set(BUILD_EXAMPLES OFF)
        set(BUILD_REGTEST OFF)
        set(ENABLE_PACKAGE_CONFIG OFF)
        set(INSTALL_PKGCONFIG_MODULE OFF)
        CPMAddPackage("${arg}" ${ARGN})
        set(BUILD_TESTING ${BUILD_TESTING_BAK})
    elseif (arg MATCHES "^gh:intel/hyperscan")
        CPMAddPackage("${arg}" ${ARGN})
        add_library(hs_impl INTERFACE)
        target_link_libraries(hs_impl INTERFACE hs)
        target_include_directories(hs_impl SYSTEM INTERFACE ${hyperscan_SOURCE_DIR}/src)
        target_include_directories(hs_impl SYSTEM INTERFACE ${hyperscan_BINARY_DIR})
        add_library(hs::hs ALIAS hs_impl)
    elseif (arg MATCHES "^https://www.akenotsuki.com/misc/srell/releases/srell4")
        CPMAddPackage("${arg}" ${ARGN})
        add_library(srell INTERFACE)
        target_include_directories(srell SYSTEM INTERFACE ${srell4_SOURCE_DIR})
        add_library(srell::srell ALIAS srell)
    elseif (arg MATCHES "^gh:antirez/linenoise")
        CPMAddPackage("${arg}" ${ARGN})
        add_library(linenoise ${linenoise_SOURCE_DIR}/linenoise.c)
        target_include_directories(linenoise PUBLIC ${linenoise_SOURCE_DIR})
    elseif (arg MATCHES "^gh:jarro2783/cxxopts")
        set(CXXOPTS_BUILD_EXAMPLES OFF)
        set(CXXOPTS_BUILD_TESTS OFF)
        CPMAddPackage("${arg}" ${ARGN})
    elseif (arg MATCHES "^gh:Cyan4973/xxHash")
        CPMAddPackage("${arg}" ${ARGN})
        add_library(xxhash ${xxHash_SOURCE_DIR}/xxhash.c ${xxHash_SOURCE_DIR}/xxh_x86dispatch.c)
        target_include_directories(xxhash SYSTEM INTERFACE $<BUILD_INTERFACE:${xxHash_SOURCE_DIR}>)
    elseif (arg MATCHES "^gh:rikyoz/7-Zip")
        CPMAddPackage("${arg}" ${ARGN})
        file(GLOB 7-Zip_SOURCE_FILES "${7-Zip_SOURCE_DIR}/C/*.c")
        add_library(7-Zip_impl ${7-Zip_SOURCE_FILES})
        target_include_directories(7-Zip_impl PUBLIC SYSTEM
            "${7-Zip_SOURCE_DIR}/CPP/"
            "${7-Zip_SOURCE_DIR}/C/"
        )
        add_library(7-zip::7-zip ALIAS 7-Zip_impl)
        set(BIT7Z_CUSTOM_7ZIP_PATH "${7-Zip_SOURCE_DIR}" PARENT_SCOPE)
    elseif (arg MATCHES "^gh:xxsds/sdsl-lite")
        if ("${ARGN}" MATCHES "^$")
            if (arg MATCHES "@")
                string(REPLACE "@" ";" L ${arg})
                list(GET L 1 VERSION)
                CPMAddPackage(NAME sdsl-lite
                    GITHUB_REPOSITORY xxsds/sdsl-lite
                    VERSION ${VERSION}
                    DOWNLOAD_ONLY YES
                )
            elseif(arg MATCHES "#")
                string(REPLACE "#" ";" L ${arg})
                list(GET L 1 GIT_TAG)
                CPMAddPackage(NAME sdsl-lite
                    GITHUB_REPOSITORY xxsds/sdsl-lite
                    GIT_TAG ${GIT_TAG}
                    DOWNLOAD_ONLY YES
                )
            else()
                message(FATAL_ERROR "WrappedCPMPackage can not parse ${arg}")
            endif()
        else()
            CPMAddPackage("${arg}"
                ${ARGN}
                DOWNLOAD_ONLY YES
            )
        endif()
        add_library(sdsl-lite INTERFACE)
        target_include_directories(sdsl-lite INTERFACE SYSTEM
            "${sdsl-lite_SOURCE_DIR}/include"
        )
        add_library(sdsl-lite::sdsl-lite ALIAS sdsl-lite)
    else()
        message(FATAL_ERROR "WrappedCPMAddPackage doesn't know how to handle ${arg}")
    endif()

endfunction()
