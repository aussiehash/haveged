## Process this file with autoconf to produce a configure script.

## Minimum Autoconf version

AC_PREREQ([2.59])
AC_INIT([haveged],[1.9.4])
AC_CONFIG_AUX_DIR(config)
AC_USE_SYSTEM_EXTENSIONS
AC_CONFIG_HEADER([config.h])
AM_INIT_AUTOMAKE([subdir-objects no-dependencies])
AC_CONFIG_SRCDIR([src/haveged.c])
AC_CHECK_TYPES([uint32_t, uint8_t])
HA_LDFLAGS=""

##libtool_start##
LT_INIT
AC_PROG_LIBTOOL
AM_ENABLE_SHARED
AM_DISABLE_STATIC
LT_LANG([C])
HAVEGE_LT_VERSION="1:1:0"
AC_SUBST(HAVEGE_LT_VERSION)
AC_DEFINE_UNQUOTED(HAVEGE_LIB_VERSION, "$HAVEGE_LT_VERSION", [Library version])
##libtool_end##

## Make clock_gettime configurable
AC_ARG_ENABLE(clock_gettime,
   AS_HELP_STRING([--enable-clock_gettime=[no/yes]],[Enable clock_gettime [default=yes if no rdtsc]]),
   , enable_clock_gettime="x")

## Make build w/o daemon interface configurable
AC_ARG_ENABLE(daemon,
   AS_HELP_STRING([--enable-daemon=[yes/no]],[Enable daemon [default=yes if linux]]),
   , enable_daemon="x" )

## Make diagnostic modes configurable
AC_ARG_ENABLE(diagnostic,
   AS_HELP_STRING([--enable-diagnostic=[no/capture/inject/yes]],[Enable diagnostic mode [default=no]]),
   , enable_diagnostic="no")
if test "x$enable_diagnostic" = "xyes"; then
   AC_DEFINE(RAW_OUT_ENABLE, 1, [Define to 1 for capture diagnostic])
   AC_DEFINE(RAW_IN_ENABLE, 1, [Define to 1 for injection diagnostic])
   enable_daemon="no";
elif test "x$enable_diagnostic" == "xcapture"; then
   AC_DEFINE(RAW_OUT_ENABLE, 1, [Define to 1 for capture diagnostic])
   enable_daemon="no";
elif test "x$enable_diagnostic" == "xinject"; then
   AC_DEFINE(RAW_IN_ENABLE, 1, [Define to 1 for injection diagnostic])
   enable_daemon="no";
fi

## Make init configurable
AC_ARG_ENABLE(init,
   AS_HELP_STRING([--enable-init=[type]],[Enable service.* or sysv.* template [default=no]]),
   , enable_init="no" )
AC_ARG_ENABLE(initdir,
   AS_HELP_STRING([--enable-initdir=DIR], [Directory for systemd service files [default=pkg-config var if init==service.*]]),
   , enable_initdir="?")

## Make nist self-test configurable
AC_ARG_ENABLE(nistest,
   AS_HELP_STRING([--enable-nistest=[no/yes]],[Run NIST test suite [default=no]]),
   , enable_nistest="no")

## Make self test features configurable
AC_ARG_ENABLE(olt,
   AS_HELP_STRING([--enable-olt=[yes/no]],[Enable online tests [default=yes]]),
   , enable_olt="yes" )
if test "x$enable_olt" = "xyes"; then
   AC_DEFINE(ONLINE_TESTS_ENABLE, 1, [Define to 1 for online test features])
fi

## Make multi-thread feature configurable
AC_ARG_ENABLE(threads,
   AS_HELP_STRING([--enable-threads=[no/yes]],[Enable threads [default=no]]),
   , enable_threads="no")
if test "x$enable_threads" = "xyes"; then
   AC_DEFINE(NUMBER_CORES, 4, [Define maxium number of collection threads])
   HA_LDFLAGS="-pthread"
else
   AC_DEFINE(NUMBER_CORES, 1, [Define to single collection thread])
fi

## Make tuning features configurable
AC_ARG_ENABLE(tune,
   AS_HELP_STRING([--enable-tune=[yes/cpuid/vfs/no]],[Enable host tune methods [default=yes]]),
   , enable_tune="yes" )
if test "x$enable_tune" = "xyes"; then
   AC_DEFINE(TUNING_CPUID_ENABLE, 1, [Define to 1 to tune with host cpuid])
   AC_DEFINE(TUNING_VFS_ENABLE, 1, [Define to 1 to tune with host vfs])
elif test "x$enable_tune" == "xcpuid"; then
   AC_DEFINE(TUNING_CPUID_ENABLE, 1, [Define to 1 to tune with host cpuid])
elif test "x$enable_tune" == "xvfs"; then
   AC_DEFINE(TUNING_VFS_ENABLE, 1, [Define to 1 to tune wiht host vfs])
fi

## Checks for programs.
AC_PROG_CC([gcc])

## Checks for typedefs, structures, and compiler characteristics.
AC_C_CONST
AC_HEADER_TIME

## Checks for library functions.
AC_PROG_GCC_TRADITIONAL
AC_FUNC_SELECT_ARGTYPES
AC_TYPE_SIGNAL
AC_CHECK_FUNCS([__rdtsc accept accept4 bind connect execv floor getsockopt gettimeofday listen memset pow pselect recv sched_yield select send setsockopt socket sqrt])

## Checks for header files.
AC_HEADER_STDC
AC_CHECK_HEADERS(fcntl.h)
AC_CHECK_HEADERS(limits.h)
AC_CHECK_HEADERS(sched.h)
AC_CHECK_HEADERS(semaphore.h)
AC_CHECK_HEADERS(stdarg.h)
AC_CHECK_HEADERS(stddef.h)
AC_CHECK_HEADERS(stdint.h)
AC_CHECK_HEADERS(stdio.h)
AC_CHECK_HEADERS(stdlib.h)
AC_CHECK_HEADERS(string.h)
AC_CHECK_HEADERS(sys/ioctl.h)
AC_CHECK_HEADERS(sys/mman.h)
AC_CHECK_HEADERS(sys/types.h)
AC_CHECK_HEADERS(sys/socket.h)
AC_CHECK_HEADERS(sys/un.h)
AC_CHECK_HEADERS(sys/wait.h)
AC_CHECK_HEADERS(time.h)
AC_CHECK_HEADERS(sys/time.h)
AC_CHECK_HEADERS(unistd.h)
AC_CHECK_HEADERS(cpuid.h)
AC_CHECK_HEADERS(x86intrin.h)
AC_CHECK_HEADERS(syslog.h)
AC_CHECK_HEADERS(linux/random.h)

AH_TEMPLATE([HAVE_STRUCT_UCRED],[User visible structure for SCM_CREDENTIALS message])
AC_TRY_LINK([#include <sys/socket.h>],
[struct ucred cred; cred.pid=0;],[AC_DEFINE([HAVE_STRUCT_UCRED],[1],[User visible structure for SCM_CREDENTIALS message])])

AC_CANONICAL_HOST

## Determine if daemon interface to be included

if test "x$enable_daemon" = "xyes"; then
   daemon_type="yes"
elif test "x$enable_daemon" = "xno"; then
   daemon_type="none"
else
   case "$host_os" in
    linux*)
      daemon_type="yes"
   ;;
    *)
      daemon_type="none"
   ;;
  esac
fi 

## Match host to havegedef macros
case "$host" in
 x86_64-*)
    AC_DEFINE(HAVE_ISA_X86, 1, [Define to 1 for x86_64])
 ;;

 i*86*-*)
    AC_DEFINE(HAVE_ISA_X86, 1, [Define to 1 for x86])
 ;;

 ia64-*)
    AC_DEFINE(HAVE_ISA_IA64, 1, [Define to 1 for ia64])
 ;;

 powerpc-*|pcc-*|powerpc64-*|ppc64-*)
    AC_DEFINE(HAVE_ISA_PPC, 1, [Define to 1 for ppc])
 ;;

 s390*-*)
    AC_DEFINE(HAVE_ISA_S390, 1, [Define to 1 for s390])
 ;;

 sparclite*-*)
    AC_DEFINE(HAVE_ISA_SPARCLITE, 1, [Define to 1 for sparclite])
 ;;

 sparc*-*)
    AC_DEFINE(HAVE_ISA_SPARC, 1, [Define to 1 for sparc])
 ;;

 *)
    if test "x$enable_clock_gettime" = "xx"; then
      enable_clock_gettime="yes"
    elif test "x$enable_clock_gettime" = "xyes"; then
      enable_clock_gettime="yes"
    else
      AC_MSG_ERROR([no timer source for host :"$host".])
    fi
    AC_DEFINE_UNQUOTED(HAVE_ISA_GENERIC, "$host", [Define generic host])
 ;;

esac

## Check clock_gettime option

if test "x$enable_clock_gettime" = "xyes"; then
    saved_libs=$LIBS
    LIB_CLOCK_GETTIME=
    AC_SEARCH_LIBS([clock_gettime], [rt posix4],
      [test "$ac_cv_search_clock_gettime" = "none required" || LIB_CLOCK_GETTIME=$ac_cv_search_clock_gettime],
      AC_MSG_ERROR([clock_gettime is required for host :"$host".]))
    LIBS=$saved_libs
    HA_LDFLAGS="$HA_LDFLAGS $LIB_CLOCK_GETTIME"
    AC_DEFINE(ENABLE_CLOCK_GETTIME, 1, [Define to 1 for clock_gettime])
fi


## Determine init type

if test "$daemon_type" = "none"; then
    AC_DEFINE(NO_DAEMON, 1, [Define to 1 to suppress daemon interface])
      init_type="none"
else
  case "$enable_init" in
    service.*)
       init_type="systemd"
     ;;
    sysv.*)
       init_type="sysv"
     ;;
    *)
       init_type="none"
     ;;
esac
fi

## Fixup install and test options

AC_SUBST(HA_DISTRO,$enable_init)
AC_SUBST(HA_UNITD,$enable_initdir)
AM_CONDITIONAL(ENABLE_BIN, test "$daemon_type" = "none")
AM_CONDITIONAL(ENABLE_SYSV, test "$init_type" = "sysv")
AM_CONDITIONAL(ENABLE_SYSTEMD, test "$init_type" = "systemd")
AM_CONDITIONAL(ENABLE_SYSTEMD_LOOKUP, test "$enable_initdir" = "?")
AM_CONDITIONAL(ENABLE_NOINIT, test "$init_type" = "none")
AM_CONDITIONAL(ENABLE_ENT_TEST, test "yes" = "yes")
AM_CONDITIONAL(ENABLE_NIST_TEST, test "$enable_nistest" = "yes")

## Set hardware dependent define for the build

AC_SUBST(HA_LDFLAGS)

AC_CONFIG_FILES([Makefile
                 src/Makefile
                 man/Makefile
                 init.d/Makefile
                 ent/Makefile
                 nist/Makefile])
AC_OUTPUT
