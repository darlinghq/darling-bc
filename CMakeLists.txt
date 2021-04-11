project(bc)

cmake_minimum_required(VERSION 3.10)

# Automatically escape macros
cmake_policy(SET CMP0005 NEW)

include(darling_exe)

include_directories(
	${CMAKE_CURRENT_SOURCE_DIR}/bc
	${CMAKE_CURRENT_SOURCE_DIR}/dc
	${CMAKE_CURRENT_SOURCE_DIR}/h
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
	lib/getopt.c
	lib/getopt1.c
	lib/number.c
	lib/vfprintf.c
)

add_darling_executable(bc
	bc/main.c
	bc/bc.c
	bc/scan.c
	bc/execute.c
	bc/load.c
	bc/storage.c
	bc/util.c
	bc/global.c
	$<TARGET_OBJECTS:bcstatic>
)

add_darling_executable(dc
	dc/dc.c
	dc/misc.c
	dc/eval.c
	dc/stack.c
	dc/array.c
	dc/numeric.c
	dc/string.c
	$<TARGET_OBJECTS:bcstatic>
)

install(TARGETS bc dc DESTINATION libexec/darling/usr/bin)

install(FILES doc/bc.1 doc/dc.1 DESTINATION libexec/darling/usr/share/man/man1)
