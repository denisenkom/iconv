configure_file(${CMAKE_CURRENT_SOURCE_DIR}/config.h.in ${CMAKE_CURRENT_BINARY_DIR}/config.h)

########### next target ###############

include_directories(. ../libcharset/include)

SET(iconv_LIB_SRCS
	iconv.c
	../libcharset/lib/localcharset.c
	relocatable.c
)

add_library(iconv SHARED ${iconv_LIB_SRCS})


ADD_DEPENDENCIES(iconv gen_flags_h gen_aliases_h gen_canonical_h gen_canonical_local_h gen_canonical_dos_h)

set_target_properties(iconv PROPERTIES VERSION 7.1.5 SOVERSION 4)
install(TARGETS iconv DESTINATION lib)