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
target_include_directories(inflate PRIVATE include igzip)
