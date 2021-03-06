dnl --------------------------------------------------------------
dnl Configure.ac for libdvdread
dnl --------------------------------------------------------------

dnl --------------------------------------------------------------
dnl Require autoconf version 2.53
dnl --------------------------------------------------------------
AC_PREREQ([2.53])

dnl --------------------------------------------------------------
dnl Making releases: dvdread_sub += 1; change DVDREAD_LT_* accordingly
dnl
dnl These are defined in m4 so they can be passed to AC_INIT
dnl --------------------------------------------------------------
m4_define([dvdread_major], [4])
m4_define([dvdread_minor], [2])
m4_define([dvdread_sub],   [0])
m4_define([dvdread_pre],   [])

AC_INIT([libdvdread], [dvdread_major.dvdread_minor.dvdread_sub[]dvdread_pre])
AC_CONFIG_SRCDIR([src/dvdread/dvd_reader.h])
m4_include([m4/ax_check_compile_flag.m4])
AM_INIT_AUTOMAKE([1.6])

dnl create a config.h file (Automake will add -DHAVE_CONFIG_H)
AC_CONFIG_HEADERS([config.h])
AC_CANONICAL_HOST

DVDREAD_MAJOR="dvdread_major"
DVDREAD_MINOR="dvdread_minor"
DVDREAD_SUB="dvdread_sub"
DVDREAD_PRE="dvdread_pre"

AC_SUBST(DVDREAD_MAJOR)
AC_SUBST(DVDREAD_MINOR)
AC_SUBST(DVDREAD_SUB)

dnl The libtool version numbers (DVDREAD_LT_*); Don't even think about faking this!
dnl
dnl immediately before every release do:
dnl ===================================
dnl if (the interface is totally unchanged from previous release)
dnl    DVDREAD_LT_REVISION ++;
dnl else { /* interfaces have been added, removed or changed */
dnl    DVDREAD_LT_REVISION = 0;
dnl    DVDREAD_LT_CURRENT ++;
dnl    if (any interfaces have been _added_ since last release)
dnl       AGE ++;
dnl    if (any interfaces have been _removed_ or _incompatibly changed_)
dnl       AGE = 0;
dnl }
dnl
dnl If you want to know more about what you are doing, here are some details:
dnl  * DVDREAD_LT_CURRENT is the current API version
dnl  * DVDREAD_LT_REVISION is an internal revision number which is increased when the API
dnl    itself did not change
dnl  * DVDREAD_LT_AGE is the number of previous API versions still supported by this library
dnl  * libtool has its own numbering scheme, because local library numbering schemes
dnl    are platform dependent
dnl  * in Linux, the library will be named
dnl    libname.so.(DVDREAD_LT_CURRENT - DVDREAD_LT_AGE).DVDREAD_LT_AGE.DVDREAD_LT_REVISION

DVDREAD_LT_CURRENT=5
DVDREAD_LT_AGE=1
DVDREAD_LT_REVISION=2

AC_SUBST(DVDREAD_LT_CURRENT)
AC_SUBST(DVDREAD_LT_AGE)
AC_SUBST(DVDREAD_LT_REVISION)

dnl --------------------------------------------------------------
dnl Make possible to build for another arch.
dnl --------------------------------------------------------------
if test x$DVDREAD_BUILD != "x"; then
  AC_MSG_RESULT(*** build forced to $DVDREAD_BUILD ***)
  build=$DVDREAD_BUILD
  host=$DVDREAD_BUILD
else
  check_athlon=yes
fi

dnl --------------------------------------------------------------
dnl Checks for programs.
dnl --------------------------------------------------------------
dnl Save CFLAGS, AC_ISC_POSIX set some unwanted default CFLAGS
saved_CFLAGS="$CFLAGS"
AC_ISC_POSIX
CFLAGS="$saved_CFLAGS"
AC_PROG_CC
AC_PROG_MAKE_SET
AC_PROG_INSTALL
AC_PROG_LN_S

dnl --------------------------------------------------------------
dnl Libtool
dnl --------------------------------------------------------------
dnl LT_PREREQ only available in libtool-2.2+
dnl LT_PREREQ([1.4.0])
AC_LIBTOOL_DLOPEN
AC_DISABLE_STATIC
AC_PROG_LIBTOOL
AC_SUBST(LIBTOOL_DEPS)
if ${CONFIG_SHELL} ./libtool --features | grep "enable static" >/dev/null; then
  STATIC="-static"
else
  STATIC=
fi
AC_SUBST(STATIC)

dnl --------------------------------------------------------------
dnl Checks for header files.
dnl --------------------------------------------------------------
AC_HEADER_STDC
AC_CHECK_HEADER(unistd.h)
AC_CHECK_HEADER(string.h)

dnl --------------------------------------------------------------
dnl Checks for typedefs, structures, and compiler characteristics.
dnl --------------------------------------------------------------
AC_C_CONST
AC_C_INLINE
AC_TYPE_OFF_T
AC_TYPE_SIZE_T
dnl AC_CHECK_TYPES([ptrdiff_t])
AC_C_BIGENDIAN

dnl -------------------------------------------------------------
dnl Check for basic *nix fonction that we may emulate on windows.
dnl -------------------------------------------------------------
case $host in
  *mingw32* | *cygwin*)
    AC_CHECK_FUNCS(gettimeofday)
    ;;
  *)
    ;;
esac

dnl ---------------------------------------------
dnl dynamic linker
dnl ---------------------------------------------
AC_ARG_ENABLE([libdvdcss], [  --enable-libdvdcss    force linking against libdvdcss])

AC_ARG_ENABLE([dlfcn],
  [AS_HELP_STRING([--enable-dlfcn],
  [use builtin dlfcn for mingw (default is auto)])],
  [use_builtin_dlfcn=$enableval],
  [use_builtin_dlfcn=no])

if [[ $use_builtin_dlfcn = "yes" ]]; then
	AC_DEFINE([USING_BUILTIN_DLFCN], [1], ["Define to 1 to use builtin dlfcn"])
fi

CSS_LIBS=""
if test x"$enable_libdvdcss" != xyes; then
dnl dlopen libdvdcss
case $host in
  *mingw32*)
    if [[ $use_builtin_dlfcn = "no" ]]; then
        AC_CHECK_LIB(c, dlopen,
                     DYNAMIC_LD_LIBS="",
                     AC_CHECK_LIB(dl, dlopen,
                                  DYNAMIC_LD_LIBS="-ldl",
                                  AC_MSG_WARN(Using builtin dlopen)))
        AC_SUBST(DYNAMIC_LD_LIBS)
    fi
    LDFLAGS="-no-undefined $LDFLAGS"
    ;;
  *cygwin*)
    LDFLAGS="-no-undefined $LDFLAGS"
    ;;
  *os2*)
    LDFLAGS="-no-undefined -Zbin-files $LDFLAGS"
    ;;
  *)
    AC_CHECK_LIB(c, dlopen,
                 DYNAMIC_LD_LIBS="",
                 AC_CHECK_LIB(dl, dlopen,
                              DYNAMIC_LD_LIBS="-ldl",
                              AC_MSG_ERROR(dynamic linker needed)))
    AC_SUBST(DYNAMIC_LD_LIBS)
    ;;
esac
else
dnl link with libdvdcss
  CSS_LIBS=-ldvdcss
  case "${host}" in
    *darwin*) CSS_LIBS="${CSS_LIBS} -Wl,-framework,CoreFoundation -Wl,-framework,IOKit"
    ;;
  esac
  AC_CHECK_LIB(dvdcss, dvdcss_interface_2,, AC_MSG_ERROR(You need libdvdcss.so.2 or later))
  AC_CHECK_HEADERS(dvdcss/dvdcss.h,, AC_MSG_ERROR(You need libdvdcss (dvdcss.h)))
fi

AC_SUBST(CSS_LIBS)


dnl ---------------------------------------------
dnl cflags
dnl ---------------------------------------------
dnl Common cflags for all platforms
CFLAGS="-O3 -Wall -D_FILE_OFFSET_BITS=64 -D_LARGEFILE64_SOURCE $CFLAGS"
DEBUG_CFLAGS="-g -DDEBUG $CFLAGS"

AC_SUBST(DEBUG_CFLAGS)

dnl ---------------------------------------------
dnl Check for bitfield compiler flag
dnl ---------------------------------------------
AX_CHECK_COMPILE_FLAG([-mno-ms-bitfields], [CFLAGS="-mno-ms-bitfields $CFLAGS"])

dnl ---------------------------------------------
dnl Check for doxygen (dynamic documentation generator)
dnl ---------------------------------------------
AC_CHECK_PROG(DOXYGEN, doxygen, doxygen, no)

dnl ---------------------------------------------
dnl Some include paths ( !!! DO NOT REMOVE !!! )
dnl ---------------------------------------------
INCLUDES='-I$(top_srcdir) $(DVDREAD_CFLAGS)'
AC_SUBST(INCLUDES)

dnl ---------------------------------------------
dnl Output configuration files
dnl ---------------------------------------------
AC_OUTPUT([
Makefile
src/Makefile
misc/Makefile
misc/dvdread-config
misc/dvdread.pc
misc/libdvdread.spec
misc/relchk.sh
m4/Makefile
])
