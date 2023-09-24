cmake_minimum_required(VERSION 3.14)

project(inflate
    VERSION 0.1.0
    LANGUAGES C CXX ASM_NASM
)

set_source_files_properties(igzip/igzip_inflate_multibinary.asm PROPERTIES LANGUAGE ASM_NASM)

add_executable(inflate
    decompress.cpp
    igzip/igzip_inflate.c
    igzip/igzip.c
    igzip/hufftables_c.c
    igzip/igzip_inflate_multibinary.asm
    igzip/igzip_decode_block_stateless_01.asm
    igzip/igzip_decode_block_stateless_04.asm
    igzip/rfc1951_lookup.asm
)
target_compile_definitions(inflate PUBLIC "NO_DEFLATE" "NO_CHECKSUM")
target_include_directories(inflate PRIVATE include igzip)

add_executable(inflate-with-checksum
    decompress.cpp
    igzip/igzip_inflate.c
    igzip/igzip.c
    igzip/hufftables_c.c
    igzip/igzip_inflate_multibinary.asm
    igzip/igzip_decode_block_stateless_01.asm
    igzip/igzip_decode_block_stateless_04.asm
    igzip/rfc1951_lookup.asm

    # CRC32
    crc/crc_base.c
    #crc/crc_base_aliases.c
    crc/crc_multibinary.asm
    crc/crc32_gzip_refl_by16_10.asm
    crc/crc32_gzip_refl_by8_02.asm
    crc/crc32_gzip_refl_by8.asm

    # Adler32 Checksum
    igzip/adler32_avx2_4.asm
    igzip/adler32_sse.asm
    igzip/adler32_base.c
)
target_compile_definitions(inflate-with-checksum PUBLIC "NO_DEFLATE" "NO_ZLIB")
target_include_directories(inflate-with-checksum PRIVATE include igzip)


set(ISAL_HOME "${CMAKE_CURRENT_SOURCE_DIR}")

add_library(isal STATIC
    # Files suffixed with _test, _perf, _example can be ignored!
    ${ISAL_HOME}/include/igzip_lib.h
    ${ISAL_HOME}/include/unaligned.h
    ${ISAL_HOME}/include/reg_sizes.asm
    ${ISAL_HOME}/include/multibinary.asm
    ${ISAL_HOME}/igzip/igzip_inflate.c
    ${ISAL_HOME}/igzip/igzip.c
    ${ISAL_HOME}/igzip/hufftables_c.c
    ${ISAL_HOME}/igzip/igzip_checksums.h
    ${ISAL_HOME}/igzip/igzip_inflate_multibinary.asm
    ${ISAL_HOME}/igzip/igzip_decode_block_stateless_01.asm
    ${ISAL_HOME}/igzip/igzip_decode_block_stateless_04.asm
    #${ISAL_HOME}/igzip/igzip_decode_block_stateless.asm
    ${ISAL_HOME}/igzip/rfc1951_lookup.asm
    ${ISAL_HOME}/igzip/igzip_wrapper.h
    ${ISAL_HOME}/igzip/static_inflate.h
    ${ISAL_HOME}/igzip/stdmac.asm

    # Compression
    #${ISAL_HOME}/igzip/igzip_base_aliases.c
    ${ISAL_HOME}/igzip/encode_df.c
    ${ISAL_HOME}/igzip/igzip_deflate_hash.asm
    ${ISAL_HOME}/igzip/igzip_icf_base.c
    ${ISAL_HOME}/igzip/igzip_icf_body.c
    ${ISAL_HOME}/igzip/igzip_base.c
    ${ISAL_HOME}/igzip/igzip_body.asm
    ${ISAL_HOME}/igzip/igzip_multibinary.asm
    ${ISAL_HOME}/igzip/igzip_update_histogram_01.asm
    ${ISAL_HOME}/igzip/igzip_update_histogram_04.asm
    #${ISAL_HOME}/igzip/igzip_update_histogram.asm

    #${ISAL_HOME}/igzip/bitbuf2.asm
    #${ISAL_HOME}/igzip/data_struct2.asm
    ${ISAL_HOME}/igzip/encode_df_04.asm
    ${ISAL_HOME}/igzip/encode_df_06.asm
    #${ISAL_HOME}/igzip/heap_macros.asm
    #${ISAL_HOME}/igzip/huffman.asm
    #${ISAL_HOME}/igzip/igzip_compare_types.asm
    ${ISAL_HOME}/igzip/igzip_finish.asm
    ${ISAL_HOME}/igzip/igzip_gen_icf_map_lh1_04.asm
    ${ISAL_HOME}/igzip/igzip_gen_icf_map_lh1_06.asm
    ${ISAL_HOME}/igzip/igzip_icf_body_h1_gr_bt.asm
    ${ISAL_HOME}/igzip/igzip_icf_finish.asm
    ${ISAL_HOME}/igzip/igzip_set_long_icf_fg_04.asm
    ${ISAL_HOME}/igzip/igzip_set_long_icf_fg_06.asm
    #${ISAL_HOME}/igzip/inflate_data_structs.asm
    #${ISAL_HOME}/igzip/lz0a_const.asm
    #${ISAL_HOME}/igzip/options.asm
    ${ISAL_HOME}/igzip/proc_heap.asm
    #${ISAL_HOME}/igzip/rfc1951_lookup.asm

    ${ISAL_HOME}/igzip/adler32_avx2_4.asm
    ${ISAL_HOME}/igzip/adler32_sse.asm
    ${ISAL_HOME}/igzip/adler32_base.c
    #${ISAL_HOME}/igzip/encode_df.c
    ${ISAL_HOME}/igzip/flatten_ll.c
    #${ISAL_HOME}/igzip/generate_custom_hufftables.c
    #${ISAL_HOME}/igzip/generate_static_inflate.c
    ${ISAL_HOME}/igzip/huff_codes.c
    #${ISAL_HOME}/igzip/hufftables_c.c
    #${ISAL_HOME}/igzip/igzip_base_aliases.c
    #${ISAL_HOME}/igzip/igzip_base.c
    #${ISAL_HOME}/igzip/igzip_icf_base.c
    #${ISAL_HOME}/igzip/igzip_icf_body.c
    #${ISAL_HOME}/igzip/proc_heap_base.c

    ${ISAL_HOME}/crc/crc_multibinary.asm
    ${ISAL_HOME}/crc/crc32_gzip_refl_by16_10.asm
    ${ISAL_HOME}/crc/crc32_gzip_refl_by8_02.asm
    ${ISAL_HOME}/crc/crc32_gzip_refl_by8.asm
    ${ISAL_HOME}/crc/crc_base.c
    #${ISAL_HOME}/crc/crc_base_aliases.c
)
set_target_properties(isal PROPERTIES LANGUAGE "C ASM_NASM")
target_include_directories(isal
    PUBLIC
        ${ISAL_HOME}/include
    PRIVATE
        ${ISAL_HOME}/igzip
)


add_executable(compress compress.cpp)
target_compile_features(compress PRIVATE cxx_std_17)
target_link_libraries(compress isal)
