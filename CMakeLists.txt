project(bc)

cmake_minimum_required(VERSION 3.10)

# Automatically escape macros
cmake_policy(SET CMP0005 NEW)

include(darling_exe)

include_directories(
	${CMAKE_CURRENT_SOURCE_DIR}/bc/bc
	${CMAKE_CURRENT_SOURCE_DIR}/bc/dc
	${CMAKE_CURRENT_SOURCE_DIR}/bc/h
	${CMAKE_CURRENT_SOURCE_DIR}/gen
)

add_definitions(
	-DHAVE_CONFIG_H
)

# Hide warnings
add_definitions(
	-Wno-pointer-sign
	-Wno-implicit-function-declaration
)

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -nostdinc -fwrapv -fPIC -funsigned-char")

add_darling_object_library(bcstatic
	bc/lib/getopt.c
	bc/lib/getopt1.c
	bc/lib/number.c
	bc/lib/vfprintf.c
)

add_darling_executable(bc
	bc/bc/main.c
	bc/bc/bc.c
	bc/bc/scan.c
	bc/bc/execute.c
	bc/bc/load.c
	bc/bc/storage.c
	bc/bc/util.c
	bc/bc/global.c
	$<TARGET_OBJECTS:bcstatic>
)

add_darling_executable(dc
	bc/dc/dc.c
	bc/dc/misc.c
	bc/dc/eval.c
	bc/dc/stack.c
	bc/dc/array.c
	bc/dc/numeric.c
	bc/dc/string.c
	$<TARGET_OBJECTS:bcstatic>
)

install(TARGETS bc dc DESTINATION libexec/darling/usr/bin)

install(FILES bc/doc/bc.1 bc/doc/dc.1 DESTINATION libexec/darling/usr/share/man/man1)
