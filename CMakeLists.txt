# SPDX-FileCopyrightText: 2024 Simon Gene Gottlieb
# SPDX-License-Identifier: CC0-1.0

cmake_minimum_required(VERSION 3.14 FATAL_ERROR)

include(cmake/CPM.cmake)
function(EnableCPM arg)

    if (arg MATCHES "^gh::google/googletest")
        set(BUILD_GMOCK OFF)
        CPMAddPackage("${arg}")
    elseif (arg MATCHES "^gh:google/benchmark")
        set(BENCHMARK_ENABLE_TESTING OFF)
        CPMAddPackage("${arg}")
    elseif (arg MATCHES "^gh:boost-ext/reflect")
        CPMAddPackage(arg)
        add_library(reflect INTERFACE)
        target_include_directories(reflect SYSTEM INTERFACE ${reflect_SOURCE_DIR})
        add_library(reflect::reflect ALIAS reflect)
    endif()

endfunction()
