project(bc)

cmake_minimum_required(VERSION 2.4.0)

# Automatically escape macros
cmake_policy(SET CMP0005 NEW)

include(darling_exe)

include_directories(
	${CMAKE_CURRENT_SOURCE_DIR}/bc
	${CMAKE_CURRENT_SOURCE_DIR}/dc
	${CMAKE_CURRENT_SOURCE_DIR}/h
	${DARLING_TOP_DIRECTORY}/src/libc/include/FreeBSD
	${DARLING_TOP_DIRECTORY}/src/libc/gen
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
set(CMAKE_SHARED_LINKER_FLAGS "-nodefaultlibs -nostdlib -fPIC -Wl,--version-script=${DARLING_TOP_DIRECTORY}/darwin.map")
set(CMAKE_EXE_LINKER_FLAGS "-nodefaultlibs -nostdlib -fPIC -Wl,--version-script=${DARLING_TOP_DIRECTORY}/darwin.map")

SET(CMAKE_INSTALL_RPATH "${CMAKE_INSTALL_PREFIX}/${CMAKE_INSTALL_LIBDIR}/darling")
SET(CMAKE_BUILD_WITH_INSTALL_RPATH TRUE)
SET(CMAKE_INSTALL_RPATH_USE_LINK_PATH TRUE)

add_library(bcstatic STATIC
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
)
target_link_libraries(bc bcstatic)

add_darling_executable(dc
	dc/dc.c
	dc/misc.c
	dc/eval.c
	dc/stack.c
	dc/array.c
	dc/numeric.c
	dc/string.c
)
target_link_libraries(dc bcstatic)

install(TARGETS bc dc DESTINATION libexec/darling/usr/bin)

install(FILES doc/bc.1 doc/dc.1 DESTINATION libexec/darling/usr/share/man/man1)