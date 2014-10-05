configure_file(${CMAKE_CURRENT_SOURCE_DIR}/config.h.in ${CMAKE_CURRENT_BINARY_DIR}/config.h)

########### next target ###############

include_directories(.)

SET(libdir lib)

SET(iconv_LIB_SRCS
	iconv.c
	../libcharset/lib/localcharset.c
	relocatable.c
)

add_definitions(-DLIBDIR=${libdir})
add_definitions(-DBUILDING_LIBICONV)
add_definitions(-DBUILDING_DLL)
add_definitions(-DENABLE_RELOCATABLE=1)
add_definitions(-DIN_LIBRARY)
add_definitions(-DINSTALL_DIR=${libdir})
add_definitions(-DNO_XMALLOC)
add_definitions(-Dset_relocation_prefix=libiconv_set_relocation_prefix)
add_definitions(-Drelocate=libiconv_relocate)

add_library(iconv SHARED ${iconv_LIB_SRCS})

set_target_properties(iconv PROPERTIES VERSION 7.1.5 SOVERSION 4)
install(TARGETS iconv DESTINATION lib)