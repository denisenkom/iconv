cmake_minimum_required(VERSION 2.8)

PROJECT(libiconv)

set(USE_MBSTATE_T 1)
set(BROKEN_WCHAR_H 0)
set(HAVE_WCHAR_T 1)
SET(libdir lib)

add_definitions(-DLIBDIR=${libdir})
add_definitions(-DBUILDING_LIBICONV)
add_definitions(-DBUILDING_DLL)
add_definitions(-DENABLE_RELOCATABLE=1)
add_definitions(-DIN_LIBRARY)
add_definitions(-DINSTALL_DIR=${libdir})
add_definitions(-DNO_XMALLOC)
add_definitions(-Dset_relocation_prefix=libiconv_set_relocation_prefix)
add_definitions(-Drelocate=libiconv_relocate)

configure_file(include/iconv.h.in include/iconv.h)
configure_file(libcharset/include/localcharset.h.in libcharset/include/localcharset.h)

ADD_EXECUTABLE(genflags lib/genflags.c)
ADD_EXECUTABLE(genaliases lib/genaliases.c)
ADD_EXECUTABLE(genaliases_dos lib/genaliases2.c)
TARGET_COMPILE_DEFINITIONS(genaliases_dos PUBLIC USE_DOS)
ADD_EXECUTABLE(gentranslit lib/gentranslit.c)

add_custom_command(OUTPUT lib/flags.h
	COMMAND genflags > lib/flags.h
	DEPENDS lib/encodings.def lib/encodings_aix.def lib/encodings_osf1.def lib/encodings_dos.def lib/encodings_extra.def
	)

ADD_CUSTOM_COMMAND(OUTPUT tmp.h lib/canonical.h lib/canonical_local.h
	COMMAND genaliases lib/aliases.gperf canonical.sh canonical_local.sh
	COMMAND gperf -m 10 lib/aliases.gperf > tmp.h
	COMMAND sh canonical.sh > lib/canonical.h
	COMMAND sh canonical_local.sh > lib/canonical_local.h
	DEPENDS lib/encodings.def lib/encodings_local.def
	)

ADD_CUSTOM_COMMAND(OUTPUT lib/aliases_dos.h lib/canonical_dos.h
	COMMAND genaliases_dos dos lib/aliases_dos.h lib/canonical_dos.h
	)

ADD_CUSTOM_COMMAND(OUTPUT lib/translit.h
	COMMAND gentranslit < lib/translit.def > lib/translit.h
	)


ADD_CUSTOM_TARGET(gen_flags_h DEPENDS lib/flags.h)
ADD_CUSTOM_TARGET(gen_canonical_headers DEPENDS lib/aliases.h lib/canonical.h lib/canonical_local.h)
ADD_CUSTOM_TARGET(gen_canonical_dos_h DEPENDS lib/canonical_dos.h)
ADD_CUSTOM_TARGET(gen_translit_h DEPENDS lib/translit.h)

include_directories(include)

ADD_SUBDIRECTORY(lib)
ADD_SUBDIRECTORY(preload)
ADD_SUBDIRECTORY(srclib)
ADD_SUBDIRECTORY(src)
ADD_SUBDIRECTORY(tests)