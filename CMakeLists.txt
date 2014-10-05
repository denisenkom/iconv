cmake_minimum_required(VERSION 2.8)

PROJECT(libiconv)

set(USE_MBSTATE_T 1)
set(BROKEN_WCHAR_H 0)
set(HAVE_WCHAR_T 1)

configure_file(include/iconv.h.in include/iconv.h)
configure_file(libcharset/include/localcharset.h.in libcharset/include/localcharset.h)

ADD_EXECUTABLE(genflags lib/genflags.c)
ADD_EXECUTABLE(genaliases lib/genaliases.c)
ADD_EXECUTABLE(genaliases_dos lib/genaliases2.c)
TARGET_COMPILE_DEFINITIONS(genaliases_dos PUBLIC USE_DOS)

add_custom_command(OUTPUT lib/flags.h
	COMMAND genflags > lib/flags.h
	)

ADD_CUSTOM_COMMAND(OUTPUT lib/aliases.gperf canonical.sh canonical_local.sh
	COMMAND genaliases lib/aliases.gperf canonical.sh canonical_local.sh
 )

ADD_CUSTOM_COMMAND(OUTPUT lib/aliases.h
	COMMAND gperf -m 10 lib/aliases.gperf > lib/aliases.h
	MAIN_DEPENDENCY lib/aliases.gperf
	)

ADD_CUSTOM_COMMAND(OUTPUT lib/canonical.h
	COMMAND sh canonical.sh > lib/canonical.h
	MAIN_DEPENDENCY canonical.sh
 )

ADD_CUSTOM_COMMAND(OUTPUT lib/canonical_local.h
	COMMAND sh canonical_local.sh > lib/canonical_local.h
	MAIN_DEPENDENCY canonical_local.sh
 )

ADD_CUSTOM_COMMAND(OUTPUT lib/aliases_dos.h lib/canonical_dos.h
	COMMAND genaliases_dos dos lib/aliases_dos.h lib/canonical_dos.h
	)


ADD_CUSTOM_TARGET(gen_flags_h DEPENDS lib/flags.h)
ADD_CUSTOM_TARGET(gen_aliases_h DEPENDS lib/aliases.h)
ADD_CUSTOM_TARGET(gen_canonical_h DEPENDS lib/canonical.h)
ADD_CUSTOM_TARGET(gen_canonical_dos_h DEPENDS lib/canonical_dos.h)
ADD_CUSTOM_TARGET(gen_canonical_local_h DEPENDS lib/canonical_local.h)

include_directories(include)

add_subdirectory(lib)