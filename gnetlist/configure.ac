# Process this file with autoconf to produce a configure script.
AC_INIT
AC_CONFIG_SRCDIR([src/gnetlist.c])
AC_PREREQ(2.54)

PACKAGE=geda-gnetlist
VERSION=20031231
echo Configuring $PACKAGE version $VERSION

# Initialize automake 
AM_INIT_AUTOMAKE($PACKAGE, $VERSION)
AM_CONFIG_HEADER([config.h])

# Initialize maintainer mode
AM_MAINTAINER_MODE


#########################################################################
# Command line flags start
# 
# Change default location for rc files
AC_ARG_WITH(rcdir, [  --with-rcdir=path       Change where the system-*rc files are installed], [opt_rcdir=$withval])

# Force a specific version of gtk+
AC_ARG_WITH(gtk12, [  --with-gtk12            Force the use of gtk+ 1.2.x], [opt_gtkver=1.2])

# 
# Command line flags end
#########################################################################

# Checks for programs.
AC_PROG_CC
AC_PROG_CPP
AC_PROG_MAKE_SET
AC_PATH_PROGS(AWK, nawk gawk mawk awk, )

#########################################################################
# 
# Misc win32 / mingw checks and variables start
AC_CANONICAL_HOST

# Figure out if we are building on win32 and what environment.
case $host_os in
  *cygwin*  ) AC_MSG_ERROR([Building gEDA/gaf under cygwin is not supported]) ;;
  *mingw32* ) echo "Configuring for mingw"; MINGW=yes ;;
esac

if ! test "$MINGW" = "no" -o "$MINGW"x = x; then
   MINGW="yes"
   PATHSEP=\\\\
   OTHERPATHSEP=/
else
   # Unix host
   MINGW="no"
   PATHSEP=/
   OTHERPATHSEP=\\\\
fi

# 
# Misc win32 / mingw checks and variables end
#########################################################################

############################################################################
# Check for mics things start
# 
# Checking for rint in math library
AC_CHECK_LIB(m, rint, AC_DEFINE(HAS_RINT, 1, [If your math library has rint in it, define this]), no_RINT="yes")

# Checking for dynamic lib
AC_CHECK_LIB(dl, dlopen, DL_LIB="-ldl", DL_LIB="")
#
# Check for mics things start
############################################################################

############################################################################
# Check for gtk+ 1.2 and 2.2 start
# 

# Check for pkg-config
AC_PATH_PROG(PKG_CONFIG, pkg-config, no)
if test $PKG_CONFIG = no; then
   AC_MSG_ERROR([Cannot find pkg-config, make sure it is installed and in your PATH])
fi

# Search for gtk+ 2.2.x first (only if we are not forcing the gtk+ to 1.2)
if test "$opt_gtkver" != "1.2"
then
   PKG_CHECK_MODULES(GTK22, gtk+-2.0 >= 2.2.0, GTK22="yes", no_GTK22="yes")
fi

# This next bit of code figures out what gtk we need to use.
if test "$GTK22" = "yes" -a "$opt_gtkver" != "1.2"
then

   AC_DEFINE(HAS_GTK22, 1, [If gtk+ 2.2.x has been installed, define this])
   GTK_CFLAGS=$GTK22_CFLAGS
   GTK_LIBS=$GTK22_LIBS
   GTK_VERSION=`$PKG_CONFIG gtk+-2.0 --modversion`

   # Search for glib
   PKG_CHECK_MODULES(GLIB22, glib-2.0 >= 2.2.0, GLIB22="yes", no_GLIB22="yes")
   if test "$GLIB22" != "yes"
   then
       AC_MSG_ERROR([Cannot find glib 2.2.x, install it and rerun ./configure.])
   fi
   GLIB_CFLAGS=$GLIB22_CFLAGS
   GLIB_LIBS=$GLIB22_LIBS
   GLIB_VERSION=`$PKG_CONFIG glib-2.0 --modversion`

else

   # Search for gtk+ 1.2 now 
   PKG_CHECK_MODULES(GTK12, gtk+ >= 1.2.3, GTK12="yes", no_GTK12="yes")

   AC_DEFINE(HAS_GTK12, 1, [If gtk+ 1.2.x has been installed, define this])
   GTK_CFLAGS=$GTK12_CFLAGS
   GTK_LIBS=$GTK12_LIBS
   GTK_VERSION=`$PKG_CONFIG gtk+ --modversion`

   # Search for glib
   PKG_CHECK_MODULES(GLIB12, glib >= 1.2.3, GLIB12="yes", no_GLIB12="yes")
   if test "$GLIB12" != "yes"
   then
       AC_MSG_ERROR([Cannot find glib >= 1.2.3, install it and rerun ./configure.])
   fi
   GLIB_CFLAGS=$GLIB12_CFLAGS
   GLIB_LIBS=$GLIB12_LIBS
   GLIB_VERSION=`$PKG_CONFIG glib --modversion`
fi

if test "$GTK_VERSION" = ""
then
   AC_MSG_ERROR([Cannot find gtk+ 2.2.x or gtk+ 1.2.x, install one of them.])
fi

# 
# Check for gtk+ 1.2 and 2.2 end
############################################################################

############################################################################
# Check for libgeda start
# 
PKG_CHECK_MODULES(LIBGEDA, libgeda >= $VERSION, LIBGEDA="yes", 
                  no_LIBGEDA="yes")

if test "$LIBGEDA" = "yes" 
then
   LIBGEDA_VERSION=`$PKG_CONFIG libgeda --modversion`
else
   AC_MSG_ERROR([Cannot find libgeda (or libgeda.pc), please install libgeda])
fi

# 
# Check for libgeda end
############################################################################

#########################################################################
# Checks for header files start
# 
AC_HEADER_STDC
AC_HEADER_SYS_WAIT
AC_HEADER_DIRENT
AC_CHECK_HEADERS(unistd.h string.h stdlib.h \
                 stdarg.h assert.h fcntl.h errno.h)

# Checks for typedefs, structures, and compiler characteristics.
AC_C_CONST

# Checks for library functions.
AC_TYPE_SIGNAL
AC_CHECK_FUNCS(getcwd strstr vsnprintf snprintf)

AC_MSG_CHECKING([for optarg in unistd.h])
AC_TRY_COMPILE(
[#include <unistd.h>],
[ char *string = optarg; int i = optind; ],
optarg_found=yes,
optarg_found=no)
AC_MSG_RESULT($optarg_found)

if test $optarg_found = yes; then
    AC_DEFINE(OPTARG_IN_UNISTD, 1, [Define if you have optarg in unistd.h])
fi

# 
# Checks for header files start
#########################################################################

#########################################################################
# Check for groff start
#

# search for groff
AC_PATH_PROG(GROFF, groff, no, ${PATH})
if test $GROFF = "no"; then
        echo "Cannot find groff, some documentation will not be created."
fi

# 
# Check for groff end
#########################################################################

#########################################################################
# gEDA/gaf specify setup start
#

DATADIR=gEDA
GEDADATADIR=$datadir/$DATADIR

if eval "test x$opt_rcdir = x"; then
	# path not was specified with --with-rcdir
        AC_DEFINE_UNQUOTED(GEDARCDIR, "none", [gEDA/gaf's rc directory])
	GEDARCDIR=$GEDADATADIR
else
	# path WAS specified with --with-rcdir
        AC_DEFINE_UNQUOTED(GEDARCDIR, "$opt_rcdir", [gEDA/gaf's rc directory])
	GEDARCDIR="$opt_rcdir"
fi

# Expand the prefix variable
# I don't like the way this is done, but it works (I hope).
if eval "test x$prefix = xNONE"; then
 dprefix=$ac_default_prefix
else
 dprefix=$prefix
fi

gedatopdir=$dprefix/share/$DATADIR
expandgedadatadir=`echo $gedatopdir`

gedadocdir=$dprefix/share/doc/geda-doc
# --with-docdir : tells where to store documentation if not default
AC_ARG_WITH(docdir,
    [  --with-docdir      Where to store documentation if not default.],
        gedadocdir=$withval)
expandgedadocdir=`echo $gedadocdir`
GEDADOCDIR=$expandgedadocdir
AC_SUBST(GEDADOCDIR)

# this has to be expanded ( no ${prefix} ) --
AC_DEFINE_UNQUOTED(GEDADATADIR, "$gedatopdir", [gEDA/gaf's data directory])
AC_DEFINE_UNQUOTED(VERSION, "$VERSION", [Currently running version of gEDA/gaf])

#
# gEDA/gaf specify things which need to setup
#########################################################################

# Finally create the final CFLAGS and LDFLAGS for use in the makefiles
GNETLIST_CFLAGS="$LIBGEDA_CFLAGS"
GNETLIST_LDFLAGS="$LIBGEDA_LIBS"  

# Makefile.in variable substitution
AC_SUBST(GNETLIST_CFLAGS)
AC_SUBST(GNETLIST_LDFLAGS)
AC_SUBST(GEDARCDIR)
AC_SUBST(GEDADATADIR)
AC_SUBST(PATHSEP)
AC_SUBST(OTHERPATHSEP)

# Create all the necessary derived files
AC_CONFIG_FILES([Makefile 
		 src/Makefile 
		 include/Makefile 
		 scheme/Makefile 
		 examples/Makefile 
		 tests/Makefile 
		 tests/hierarchy/Makefile 
		 docs/Makefile 
		 docs/vams/Makefile 
		 examples/vams/Makefile 
		 examples/vams/sch/Makefile 
		 examples/vams/sym/Makefile 
		 examples/vams/vhdl/Makefile 
		 examples/vams/vhdl/basic-vhdl/Makefile 
		 examples/vams/vhdl/new-vhdl/Makefile 
		 examples/switcap/Makefile 
		 lib/Makefile 
		 utils/Makefile 
		 scripts/Makefile
		 scripts/sw2asc 
		 lib/system-gnetlistrc ])
AC_CONFIG_COMMANDS([sw2asc-chmod],[[chmod +x scripts/sw2asc]],[[]])

AC_OUTPUT

expandedGEDADATADIR=`eval "echo $GEDADATADIR"`
expandedGEDARCDIR=`eval "echo $GEDARCDIR"`
expandedGEDADOCDIR=`eval "echo $GEDADOCDIR"`

AC_MSG_RESULT([
** Configuration summary for $PACKAGE $VERSION:

   GTK+ library version:             $GTK_VERSION
   libgeda library version:          $LIBGEDA_VERSION
   mingw build:                      $MINGW
   data directory:                   $expandedGEDADATADIR
   rc directory:                     $expandedGEDARCDIR
   documentation directory:          $expandedGEDADOCDIR
])
