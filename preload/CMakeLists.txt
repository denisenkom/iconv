SET(SOURCES
	../lib/iconv.c
	../libcharset/lib/localcharset.c
	../lib/relocatable.c)

INCLUDE_DIRECTORIES(../lib ../include .. ../libcharset/include)

ADD_LIBRARY(preloadable_libiconv SHARED ${SOURCES})

TARGET_COMPILE_DEFINITIONS(preloadable_libiconv PUBLIC LIBICONV_PLUG)
ADD_DEPENDENCIES(preloadable_libiconv ../lib/config.h)