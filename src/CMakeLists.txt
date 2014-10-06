SET(SRCS iconv_no_i18n.c)

INCLUDE_DIRECTORIES(. .. ../include ../srclib ../lib)

IF(WIN32)
	SET(SRCS ${SRCS} ../windows/iconv.rc)
ENDIF(WIN32)

ADD_EXECUTABLE(iconv_no_i18n ${SRCS})
TARGET_COMPILE_DEFINITIONS(iconv_no_i18n PUBLIC INSTALLDIR=${bindir} LOCALEDIR=${localedir})
