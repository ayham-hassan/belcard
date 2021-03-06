#                                               -*- Autoconf -*-
# Process this file with autoconf to produce a configure script.

AC_PREREQ([2.69])
AC_INIT([belcard], [0.0.1], [sylvain.berfini@belledonne-communications.com])
AC_CONFIG_SRCDIR([src/])
AC_CONFIG_HEADERS([config.h])

AM_INIT_AUTOMAKE
AM_SILENT_RULES(yes)
LT_INIT(disable-static win32-dll)

# Checks for programs.
AC_PROG_CXX

CXXFLAGS="$CXXFLAGS -std=c++11 -Wall -Werror"

# Checks for libraries.
dnl ##################################################
dnl # Check for XXD
dnl ##################################################

AC_CHECK_PROG(xxd_found, xxd, yes)
if test "$xxd_found" != yes ;then
        AC_MSG_ERROR("xxd is required (provided by vim package)")
fi

dnl ##################################################
dnl # Check for BcToolbox
dnl ##################################################

dnl check bctoolbox
PKG_CHECK_MODULES(BCTOOLBOXTESTER, [bctoolbox-tester],[libbctoolbox_tester_found=yes],foo=bar)
if test "$libbctoolbox_tester_found" != "yes" ; then
        dnl Check the lib presence in case the PKG-CONFIG version is not found
        AC_LANG_CPLUSPLUS
        AC_CHECK_LIB(bctoolbox-tester, main, [BCTOOLBOX_TESTER_LIBS+=" -lbctoolbox-tester"; libbctoolbox_tester_found=yes], [foo=bar])
        AC_LANG_C
fi
if test "$libbctoolbox_tester_found" != "yes" ; then
        AC_MSG_ERROR([libbctoolbox tester not found. Install it and try again])
fi

AC_SUBST(BCTOOLBOX_TESTER_LIBS)

dnl ##################################################
dnl # Check for Belr
dnl ##################################################

dnl check belr
PKG_CHECK_MODULES(BELR, [libbelr],[libbelr_found=yes],foo=bar)
if test "$libbelr_found" != "yes" ; then
	dnl Check the lib presence in case the PKG-CONFIG version is not found
	AC_LANG_CPLUSPLUS
	AC_CHECK_LIB(belr, main, [BELR_LIBS+=" -lbelr"; libbelr_found=yes], [foo=bar])
	AC_LANG_C
fi
if test "$libbelr_found" != "yes" ; then
	AC_MSG_ERROR([libbelr not found. Install it and try again])
fi
	
AC_SUBST(BELR_LIBS)

dnl ##################################################
dnl # Check for CUnit
dnl ##################################################

PKG_CHECK_MODULES(CUNIT, cunit, [found_cunit=yes],[found_cunit=no])

if test "$found_cunit" = "no" ; then
	AC_CHECK_HEADERS(CUnit/CUnit.h,
		[
			AC_CHECK_LIB(cunit,CU_add_suite,[
				found_cunit=yes
				CUNIT_LIBS+=" -lcunit"
			])
		])
fi
case "$target_os" in
	*darwin*)
		#hack for macport
		CUNIT_LIBS+=" -lncurses"
	;;
	*mingw*)
		CPPFLAGS="$CPPFLAGS -D_WIN32_WINNT=0x0501"
		LIBS="$LIBS -lws2_32 -liphlpapi"
		LDFLAGS="$LDFLAGS -Wl,--export-all-symbols"
	;;
esac

if test "$found_cunit" = "no" ; then
	AC_MSG_WARN([Could not find cunit framework, tests are not compiled.])
else
	AC_CHECK_LIB(cunit,CU_get_suite,[
					AC_DEFINE(HAVE_CU_GET_SUITE,1,[defined when CU_get_suite is available])
					],[foo=bar],[$CUNIT_LIBS])
	AC_CHECK_LIB(cunit,CU_curses_run_tests,[
					AC_DEFINE(HAVE_CU_CURSES,1,[defined when CU_curses_run_tests is available])
					],[foo=bar],[$CUNIT_LIBS])
fi

AM_CONDITIONAL(BUILD_TESTS,test x$found_cunit = xyes)

# Checks for header files.

# Checks for typedefs, structures, and compiler characteristics.
AC_TYPE_SSIZE_T

# Checks for library functions.
AC_CONFIG_FILES(
Makefile
src/Makefile
tester/Makefile
tools/Makefile
include/Makefile
include/belcard/Makefile
)

AC_OUTPUT
