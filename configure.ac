## ------------- ##
## Package setup ##
## ------------- ##

AC_INIT([hellocontainer], [0.1], [Ottavio Campana <ottavio@campana.vi.it>], [hellocontainer])

AC_CONFIG_AUX_DIR([build-aux])
AM_CONFIG_HEADER(config.h)

AM_INIT_AUTOMAKE([subdir-objects foreign -Wall])

## -------------------- ##
## Tool/Compilers setup ##
## -------------------- ##

# Extract host information.
AC_CANONICAL_HOST

# If you happen to have some C code:
AC_LANG([C])
AC_PROG_CC
AM_PROG_CC_C_O

# Check for some headers
AC_HEADER_STDC
AC_CHECK_HEADERS([stdio.h])

AM_CONDITIONAL([ARCHITECTURE_386], [test x"${host_cpu}" = xi386])
AM_CONDITIONAL([ARCHITECTURE_AMD64], [test x"${host_cpu}" = xx86_64])
AM_CONDITIONAL([ARCHITECTURE_ARM], [test x"${host_cpu}" = xarm])
AM_CONDITIONAL([ARCHITECTURE_ARM64], [test x"${host_cpu}" = xaarch64])
AM_CONDITIONAL([OS_LINUX], [test x"${build_os}" = xlinux-gnu])

AC_CONFIG_FILES([
  Makefile
  src/Makefile
])

AC_OUTPUT
