#                                               -*- Autoconf -*-
# Process this file with autoconf to produce a configure script.

AC_PREREQ([2.62])
AC_INIT([SQLFLAIM], [6.0], [flaim-users@lists.sourceforge.net])
AM_INIT_AUTOMAKE([-Wall -Werror])
LT_PREREQ([2.2])
LT_INIT([dlopen])

AC_LANG([C++])

AC_CONFIG_MACRO_DIR([m4])
AC_CONFIG_SRCDIR([src/flaimsql.h])
AC_CONFIG_HEADERS([config.h])

# Checks for programs.
AC_PROG_CXX
AC_PROG_INSTALL

# Checks for optional programs.
FLM_PROG_TRY_DOXYGEN

# Configure FTKLIB, FTKINC, FTK_LTLIB and FTK_INCLUDE
FLM_FTK_SEARCH

# Configure options: --enable-debug[=no].
AC_ARG_ENABLE([debug],
  [AS_HELP_STRING([--enable-debug],
    [enable debug code (default is no)])],
  [debug="$withval"], [debug=no])

# Create Automake conditional based on the DOXYGEN variable
AM_CONDITIONAL([HAVE_DOXYGEN], [test "x$DOXYGEN" != x])

# Configure for large files, even on 32-bit systems
AC_SYS_LARGEFILE

# Check for pthreads
ACX_PTHREAD(
 [AC_DEFINE([HAVE_PTHREAD], [1], [Define if you have POSIX threads libraries and header files.])
  LIBS="$PTHREAD_LIBS $LIBS"
  CFLAGS="$CFLAGS $PTHREAD_CFLAGS"
  CXXFLAGS="$CXXFLAGS $PTHREAD_CXXFLAGS"
  CC="$PTHREAD_CC"
  CXX="$PTHREAD_CXX"])

# Checks for libraries.

# Checks for header files.

# Checks for typedefs, structures, and compiler characteristics.
AC_HEADER_STDBOOL
AC_C_INLINE

# Checks for library functions.

# Configure DEBUG source code, if requested.
AS_IF([test "x$debug" = xyes],
  [AC_DEFINE([FLM_DEBUG], [1], [Define to enable FLAIM debug features])])

# Configure compiler and platform flags and options.
# This stuff was in the original makefile...
# Personally, I believe -O2 does as good a job as 
# a hand-coded set of optimization flags...
gcc_opt_flags="-O -foptimize-sibling-calls
 -fstrength-reduce -fcse-follow-jumps -fcse-skip-blocks
 -frerun-cse-after-loop -frerun-loop-opt -fgcse
 -fgcse-lm -fgcse-sm -fdelete-null-pointer-checks
 -fexpensive-optimizations -fregmove -fsched-interblock
 -fsched-spec -fcaller-saves -fpeephole2 -freorder-blocks
 -freorder-functions -falign-functions -falign-jumps
 -falign-loops -falign-labels -fcrossjumping"

# Enable g++ specific compiler flags.
# Currently, enabling all warnings displays the type-pun error dozens
# of times, and of course enabling warnings as werrors breaks the build
#if test "x$CXX" = "xg++"; then
#  CXXFLAGS="$CXXFLAGS -Wall -Werror"
#  CXXFLAGS="$CXXFLAGS $gcc_opt_flags"
#fi

# Configure supported platforms' compiler and linker flags.
case $host in
  sparc-*-solaris*)
    LDFLAGS="$LDFLAGS -R /usr/lib/lwp"
    case $CXX in
      *g++*) ;;
      *)
        if "x$debug" = xno; then
          CXXFLAGS="$CXXFLAGS -xO3"
        fi
        SUN_STUDIO_COMPILER=`$CXX -V | grep "Sun C++"`
        if "x$SUN_STUDIO_COMPILER" = "xSun C++"; then
          CXXFLAGS="$CXXFLAGS -errwarn=%all -errtags -erroff=hidef,inllargeuse,doubunder"
        fi ;;
    esac ;;

  *-apple-darwin*)
    AC_DEFINE([OSX], [1], [Define if building on Apple OSX.]) ;;

  *-*-aix*)
    case $CXX in
      *g++*) ;;
      *) CXXFLAGS="$CXXFLAGS -qstrict" ;;
    esac ;;

  *-*-hpux*)
    case $CXX in
      *g++*) ;;
      *)
        # Disable "Placement operator delete invocation is not yet implemented" warning
        CXXFLAGS="$CXXFLAGS +W930" ;;
    esac ;;
esac

AC_CONFIG_FILES([Makefile
                 obs/sqlflaim.spec
                 obs/Makefile
                 src/Makefile
                 src/libflaimsql.pc])
AC_OUTPUT

echo "
  FLAIM SQL Database Library ($PACKAGE_NAME) version $PACKAGE_VERSION
  Prefix.........: $prefix
  Debug Build....: $debug
  C++ Compiler...: $CXX $CXXFLAGS $CPPFLAGS
  Linker.........: $LD $LDFLAGS $LIBS
  FTK Library....: ${FTKLIB:-INSTALLED}
  FTK Include....: ${FTKINC:-INSTALLED}
  Doxygen........: ${DOXYGEN:-NONE}
"
