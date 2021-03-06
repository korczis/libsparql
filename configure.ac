dnl configure.ac                                        -*- Autoconf -*-
dnl
dnl Process this file with `autoconf` to produce a configure script.
dnl
dnl This is free and unencumbered software released into the public domain.
AC_PREREQ([2.68])

dnl Define version information:
m4_define([VERSION_MAJOR],
  m4_esyscmd([cut -d'.' -f1 VERSION | tr -d '\n']))
m4_define([VERSION_MINOR],
  m4_esyscmd([cut -d'.' -f2 VERSION | tr -d '\n']))
m4_define([VERSION_PATCH],
  m4_esyscmd([cut -d'.' -f3 VERSION | tr -d '\n']))
m4_define([VERSION_STRING],
  m4_esyscmd([git describe --dirty --always | tr -d '\n']))

dnl Define package information:
AC_INIT([libsparql++], [VERSION_STRING],
  [arto@bendiken.net], [libsparql++],
  [https://github.com/datagraph/libsparql])

dnl Configure Autoconf:
AC_CONFIG_HEADERS([src/config.h])
AC_CONFIG_SRCDIR([src/sparql++.h])
AC_CONFIG_AUX_DIR([etc/aclocal])
AC_CONFIG_MACRO_DIR([etc/aclocal])
AC_CONFIG_LIBOBJ_DIR([lib])

dnl Configure Automake:
AM_INIT_AUTOMAKE([foreign -Wall -Werror dist-bzip2 subdir-objects nostdinc])
AM_SILENT_RULES([yes])

dnl Check for programs:
AC_PROG_CC(clang gcc cc)
AC_PROG_CC_C99
AM_PROG_CC_C_O
AC_PROG_CPP
AC_PROG_CXX(clang++ g++ c++)
AC_PROG_CXXCPP
AC_USE_SYSTEM_EXTENSIONS
m4_ifdef([AM_PROG_AR], [AM_PROG_AR])
LT_INIT
PKG_PROG_PKG_CONFIG([0.24])
AC_LANG([C++])
AX_CXX_COMPILE_STDCXX_11([noext])

dnl Check for configuration options:
# --with-stdlib=libstdc++|libc++
AC_ARG_WITH([stdlib],
  [AS_HELP_STRING([--with-stdlib=LIB], [specify the C++ standard library to use [default=system]])],
  [], [with_stdlib=system])
AS_IF([test "x$with_stdlib" != "xsystem"],
  [CXXFLAGS="$CXXFLAGS -stdlib=$with_stdlib"
   LDFLAGS="$LDFLAGS -stdlib=$with_stdlib"])
# --enable-debug/--disable-debug
AC_ARG_ENABLE([debug],
  [AS_HELP_STRING([--enable-debug], [build with debugging support [default=no]])],
  [], [enable_debug=no])
AS_IF([test "x$enable_debug" != "xno"],
  [AC_DEFINE([DEBUG], [1], [Enable debugging support.])],
  [AC_DEFINE([NDEBUG], [1], [Disable assertions.])])
AM_CONDITIONAL([ENABLE_DEBUG], [test "x$enable_debug" != "xno"])
# --enable-unicode/--disable-unicode
AC_ARG_ENABLE([unicode],
  [AS_HELP_STRING([--disable-unicode], [omit support for Unicode strings])])
AS_IF([test "x$enable_unicode" == "xno"],
  [AC_DEFINE([DISABLE_UNICODE], 1, [Define to disable Unicode string support.])])
# --enable-*/--disable-*
AC_DEFUN([AX_ARG_ENABLE_FORMAT], [dnl
  m4_define([SPARQL_FORMAT_ID], m4_translit([$1], [-a-z], [_A-Z]))
  AC_ARG_ENABLE([$1],
    [AS_HELP_STRING([--disable-$1], [omit support for the $2 serialization format])])
  AS_IF([test "x$enable_$1" == "xno"], [
    AC_DEFINE([DISABLE_]SPARQL_FORMAT_ID, 1, [Define to omit the $2 module.])])
  AM_CONDITIONAL([DISABLE_]SPARQL_FORMAT_ID, [test "x$enable_$1" == "xno"])
])
AX_ARG_ENABLE_FORMAT([bool], [boolean]) # --enable-bool/--disable-bool
AX_ARG_ENABLE_FORMAT([brtr], [BRTR])    # --enable-brtr/--disable-brtr
AX_ARG_ENABLE_FORMAT([csv], [CSV])      # --enable-csv/--disable-csv
AX_ARG_ENABLE_FORMAT([json], [JSON])    # --enable-json/--disable-json
AX_ARG_ENABLE_FORMAT([sse], [SSE])      # --enable-sse/--disable-sse
AX_ARG_ENABLE_FORMAT([tsv], [TSV])      # --enable-tsv/--disable-tsv
AX_ARG_ENABLE_FORMAT([xml], [XML])      # --enable-xml/--disable-xml
AX_ARG_ENABLE_FORMAT([xslt], [XSLT])    # --enable-xslt/--disable-xslt

dnl Check for libraries:
# libyajl (libyajl-dev on Ubuntu, yajl on Mac OS X + MacPorts)
AS_IF([test "x$enable_json" != "xno"], [
  AC_CHECK_HEADERS([yajl/yajl_gen.h],
    AC_SEARCH_LIBS([yajl_gen_alloc], [yajl yajl_s], [],
      AC_MSG_ERROR([*** Shared library libyajl not found; install http://lloyd.github.io/yajl/ ***])),
    AC_MSG_ERROR([*** Header file <yajl/yajl_gen.h> not found; install http://lloyd.github.io/yajl/ ***]))
])
# libxml2 (libxml2-dev on Ubuntu, libxml2 on Mac OS X + MacPorts)
AS_IF([test "x$enable_xml" != "xno"], [
  PKG_CHECK_MODULES([libxml2], [libxml-2.0 >= 2.7.8])
])
# libxqilla (libxqilla-dev on Ubuntu, xqilla on Mac OS X + MacPorts)
AS_IF([test "x$enable_xslt" != "xno"], [
  AC_CHECK_HEADERS([xqilla/xqilla-simple.hpp],
    AC_SEARCH_LIBS([createXQillaXQCImplementation], [xqilla], [],
      AC_MSG_ERROR([*** Shared library libxqilla not found; install http://xqilla.sourceforge.net/ ***])),
    AC_MSG_ERROR([*** Header file <xqilla/xqilla-simple.hpp> not found; install http://xqilla.sourceforge.net/ ***]))
])

dnl Check for header files:

dnl Check for types:

dnl Check for structures:

dnl Check for compiler characteristics:
AC_CANONICAL_HOST
AM_CPPFLAGS="$AM_CPPFLAGS -I\$(top_srcdir)/lib -I\$(top_srcdir)/src -iquote \$(srcdir)"
AM_CXXFLAGS="$AM_CXXFLAGS -Wall -Wextra -pipe"
AM_LDFLAGS="$AM_LDFLAGS"
TEST_CPPFLAGS="$AM_CPPFLAGS -DCATCH_CONFIG_MAIN"
TEST_CXXFLAGS="$AM_CXXFLAGS"
TEST_LDFLAGS="$AM_LDFLAGS"
AC_SUBST([AM_CPPFLAGS])
AC_SUBST([AM_CXXFLAGS])
AC_SUBST([AM_LDFLAGS])
AC_SUBST([TEST_CPPFLAGS])
AC_SUBST([TEST_CXXFLAGS])
AC_SUBST([TEST_LDFLAGS])

dnl Check for library functions:
AC_CHECK_FUNCS_ONCE([open_memstream])

dnl Check for system services:

dnl Generate output:
AC_CONFIG_FILES([Makefile lib/Makefile src/Makefile src/sparql++/Makefile test/Makefile])
AC_SUBST([PACKAGE_VERSION_MAJOR], ["VERSION_MAJOR"])
AC_SUBST([PACKAGE_VERSION_MINOR], ["VERSION_MINOR"])
AC_SUBST([PACKAGE_VERSION_PATCH], ["VERSION_PATCH"])
AC_CONFIG_FILES([src/sparql++/version.h])
AH_BOTTOM([#include "libcompat.h"])
AC_OUTPUT
