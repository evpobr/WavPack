cmake_minimum_required(VERSION 3.9)

project(wavpack VERSION 5.1.0)

option(ENABLE_LEGACY "Decode legacy (< 4.0) WavPack files" ON)
option(ENABLE_DSD "Enable support for WavPack DSD files" ON)

include(CheckCSourceCompiles)

check_c_source_compiles("
    int main(void) {
      return __builtin_clz(1);

    }
  "
  HAVE___BUILTIN_CLZ
)

add_library(wavpack
    include/wavpack.h
    src/wavpack_local.h
    src/wavpack_version.h
    src/decorr_tables.h
    src/common_utils.c
    src/decorr_utils.c
    src/entropy_utils.c
    src/extra1.c
    src/extra2.c
    src/open_utils.c
    src/open_filename.c
    src/open_legacy.c
    src/open_raw.c
    src/pack.c
    src/pack_dns.c
    src/pack_floats.c
    src/pack_utils.c
    src/read_words.c
    src/tags.c
    src/tag_utils.c
    src/unpack.c
    src/unpack_floats.c
    src/unpack_seek.c
    src/unpack_utils.c
    src/write_words.c
    $<$<BOOL:${ENABLE_LEGACY}>:
        ${CMAKE_CURRENT_SOURCE_DIR}/src/unpack3.h
        ${CMAKE_CURRENT_SOURCE_DIR}/src/unpack3.c
        ${CMAKE_CURRENT_SOURCE_DIR}/src/unpack3_open.c
        ${CMAKE_CURRENT_SOURCE_DIR}/src/unpack3_open.c>
    $<$<BOOL:${ENABLE_DSD}>:
        ${CMAKE_CURRENT_SOURCE_DIR}/src/pack_dsd.c
        ${CMAKE_CURRENT_SOURCE_DIR}/src/unpack_dsd.c>
    $<$<AND:$<BOOL:${WIN32}>,$<BOOL:${BUILD_SHARED_LIBS}>>:
        ${CMAKE_CURRENT_SOURCE_DIR}/wavpackdll/resource.h
        ${CMAKE_CURRENT_SOURCE_DIR}/wavpackdll/wavpackdll.rc
        ${CMAKE_CURRENT_SOURCE_DIR}/wavpackdll/wavpack.def>
)
target_compile_definitions(wavpack
    PRIVATE
        $<$<BOOL:${MSVC}>:_CRT_SECURE_NO_WARNINGS>
        $<$<BOOL:${HAVE___BUILTIN_CLZ}>:_CRT_SECURE_NO_WARNINGS>
)

set_target_properties(wavpack PROPERTIES
    VERSION 1.2.0
    SOVERSION 1
)

target_include_directories(wavpack
    PUBLIC
        include
)
