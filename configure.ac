# LIBREPORT_PARSE_WITH(PACKAGE)
# -----------------------
# For use in AC_ARG_WITH action-if-found, for packages default ON.
# * Set NO_PACKAGE=YesPlease for --without-PACKAGE
# * Unset NO_PACKAGE for --with-PACKAGE without ARG
AC_DEFUN([LIBREPORT_PARSE_WITH],
    [m4_pushdef([LIBREPORT_UC_PACKAGE], m4_toupper([$1]))dnl
    if test "$withval" = "no"; then
        NO_[]LIBREPORT_UC_PACKAGE=YesPlease
        BUILD_[]LIBREPORT_UC_PACKAGE=
    elif test "$withval" = "yes"; then
        NO_[]LIBREPORT_UC_PACKAGE=
        BUILD_[]LIBREPORT_UC_PACKAGE=yes
    else
        NO_[]LIBREPORT_UC_PACKAGE=
        BUILD_[]LIBREPORT_UC_PACKAGE=yes
    fi
    m4_popdef([LIBREPORT_UC_PACKAGE])])


AC_INIT([libreport],[m4_esyscmd(cat ./libreport-version)],[crash-catcher@fedorahosted.org])

AC_CONFIG_MACRO_DIR([m4])
gl_LD_VERSION_SCRIPT
AM_INIT_AUTOMAKE([-Wall foreign tar-ustar])
# Support silent build rules. Disable by either passing --disable-silent-rules
# to ./configure or passing V=1 to make
m4_ifdef([AM_SILENT_RULES], [AM_SILENT_RULES])
# The 'yes' argument is undocumented but it is common knowledge that it works.
AM_SILENT_RULES([yes])
m4_ifdef([AM_PROG_AR], [AM_PROG_AR])

AC_DISABLE_STATIC
LT_INIT
AC_PROG_CC
AM_PROG_CC_C_O
AC_PROG_LN_S

AC_SYS_LARGEFILE

CFLAGS="$CFLAGS -D_LARGEFILE_SOURCE -D_LARGEFILE64_SOURCE \
          -D_FILE_OFFSET_BITS=64 -fno-strict-aliasing -std=gnu99 -Wall -Wwrite-strings"

AC_ARG_ENABLE(debug,
              [AS_HELP_STRING([--enable-debug],[Enable debug information])],
              [CFLAGS="$CFLAGS -DDEBUG -ggdb -g -O0"])

dnl ****** INTERNATIONALIZATION **********************
GETTEXT_PACKAGE=libreport
AC_SUBST(GETTEXT_PACKAGE)
AC_DEFINE_UNQUOTED(GETTEXT_PACKAGE, "$GETTEXT_PACKAGE", [Package name for gettext])

AM_GNU_GETTEXT([external])
AM_GNU_GETTEXT_VERSION([0.17])

IT_PROG_INTLTOOL([0.35.0])

dnl ****** END ****************************************

AC_PATH_PROG([ASCIIDOC], [asciidoc], [no])
[if test "$ASCIIDOC" = "no"]
[then]
    [echo "The asciidoc program was not found in the search path. Please ensure"]
    [echo "that it is installed and its directory is included in the search path."]
    [echo "Then run configure again before attempting to build libreport."]
    [exit 1]
[fi]

AC_PATH_PROG([XMLTO], [xmlto], [no])
[if test "$XMLTO" = "no"]
[then]
    [echo "The xmlto program was not found in the search path. Please ensure"]
    [echo "that it is installed and its directory is included in the search path."]
    [echo "Then run configure again before attempting to build libreport."]
    [exit 1]
[fi]

AC_ARG_WITH(bugzilla,
AS_HELP_STRING([--with-bugzilla],[use Bugzilla plugin (default is YES)]),
LIBREPORT_PARSE_WITH([bugzilla]),
LIBREPORT_PARSE_WITH([bugzilla]))
AC_SUBST([BUILD_BUGZILLA])

if test -z "$NO_BUGZILLA"; then
AM_CONDITIONAL(BUILD_BUGZILLA, true)
AC_PATH_PROG([XMLRPC], [xmlrpc-c-config], [no])
[if test "$XMLRPC" = "no"]
[then]
    [echo "The xmlrpc-c-config program was not found in the search path. Please ensure"]
    [echo "that it is installed and its directory is included in the search path."]
    [echo "Then run configure again before attempting to build libreport."]
    [exit 1]
[fi]

AC_ARG_WITH([redhatbugzillacreateprivate],
            AS_HELP_STRING([--with-redhatbugzillacreateprivate="yes/no"],
                           [Whether the Red Hat Bugzilla plugin should open
                            bugs private by default ("no")]),
            [], [with_redhatbugzillacreateprivate="no"])
AC_SUBST([RED_HAT_BUGZILLA_CREATE_PRIVATE], [$with_redhatbugzillacreateprivate])

AC_ARG_WITH([redhatbugzillaprivategroups],
            AS_HELP_STRING([--with-redhatbugzillaprivategroups="CSV"],
                           [Name of groups separated by comma]),
            [], [with_redhatbugzillaprivategroups="fedora_contrib_private"])
AC_SUBST([RED_HAT_BUGZILLA_PRIVATE_GROUPS], [$with_redhatbugzillaprivategroups])


XMLRPC_CFLAGS=`xmlrpc-c-config --cflags 2> /dev/null`
XMLRPC_LIBS=`xmlrpc-c-config --libs 2> /dev/null`
AC_SUBST(XMLRPC_CFLAGS)
AC_SUBST(XMLRPC_LIBS)

XMLRPC_CLIENT_CFLAGS=`xmlrpc-c-config client --cflags 2> /dev/null`
XMLRPC_CLIENT_LIBS=`xmlrpc-c-config client --libs 2> /dev/null`
AC_SUBST(XMLRPC_CLIENT_CFLAGS)
AC_SUBST(XMLRPC_CLIENT_LIBS)

# enable bugzilla & deps translations
for FILE in `grep -e "#.*ugzilla.*" -e "#.*naconda.*" po/POTFILES.in`
do
  sed -ie "s,$FILE,${FILE#\#}," po/POTFILES.in
  sed -ie "\,^${FILE#\#}$,d" po/POTFILES.skip
done
else
AM_CONDITIONAL(BUILD_BUGZILLA, false)

# disablie bugzilla & deps translations
for FILE in `grep -e "ugzilla" -e "naconda" po/POTFILES.in`
do
  if expr "${FILE}" : "#" > /dev/null
  then
    continue
  fi

  sed -ie "s,$FILE,#$FILE," po/POTFILES.in
  grep "$FILE" po/POTFILES.skip > /dev/null 2>&1
  if test $?
  then
    echo "$FILE" >> po/POTFILES.skip
  fi
done
fi dnl end NO_BUGZILLA

AC_ARG_WITH(mantisbt,
AS_HELP_STRING([--with-mantisbt],[use MantisBT plugin (default is YES)]),
LIBREPORT_PARSE_WITH([mantisbt]))

if test -z "$NO_MANTISBT"; then
AM_CONDITIONAL(BUILD_MANTISBT, true)

# enable mantisbt & deps translations
for FILE in `grep -e "#.*antisbt.*" -e "#.*naconda.*" po/POTFILES.in`
do
  sed -ie "s,$FILE,${FILE#\#}," po/POTFILES.in
  sed -ie "\,^${FILE#\#}$,d" po/POTFILES.skip
done
else
AM_CONDITIONAL(BUILD_MANTISBT, false)

# disablie mantisbt & deps translations
for FILE in `grep -e "antisbt" -e "naconda" po/POTFILES.in`
do
  if expr "${FILE}" : "#" > /dev/null
  then
    continue
  fi

  sed -ie "s,$FILE,#$FILE," po/POTFILES.in
  grep "$FILE" po/POTFILES.skip > /dev/null 2>&1
  if test $?
  then
    echo "$FILE" >> po/POTFILES.skip
  fi
done
fi dnl end NO_MANTISBT


AC_ARG_WITH([python3],
            [AS_HELP_STRING([--with-python3],
                            [use python3 (default is YES)])],
            [LIBREPORT_PARSE_WITH([python3])])

AM_CONDITIONAL([BUILD_PYTHON3], [test -z "$NO_PYTHON3"])

if test -z "$NO_PYTHON3"; then
    AM_PATH_PYTHON([3.6],,
                   [AC_MSG_ERROR([
    The python3 program was not found in the search path. Please ensure
    that it is installed and its directory is included in the search path or
    pass --without-python3 to ./configure.
    Then run configure again before attempting to build libreport.
                   ])])

    AC_PATH_PROG([PYTHON3_CONFIG], [python${PYTHON_VERSION}-config], [no])
    [if test "$PYTHON3_CONFIG" = "no"]
    [then]
        [echo "The python${PYTHON_VERSION}-config program was not found in the search path. Please ensure"]
        [echo "that it is installed and its directory is included in the search path or"]
        [echo "pass --without-python3 to ./configure."]
        [echo "Then run configure again before attempting to build libreport."]
        [exit 1]
    [fi]

    PYTHON3_CFLAGS=`${PYTHON3_CONFIG} --cflags 2> /dev/null`
    PYTHON3_LIBS=`${PYTHON3_CONFIG} --libs 2> /dev/null`

    AC_SUBST([PYTHON3_CFLAGS])
    AC_SUBST([PYTHON3_LIBS])

    AC_SUBST([py3execdir], [$pyexecdir])
fi dnl end NO_PYTHON3

m4_define([glib_version], [2.43.4])

PKG_CHECK_MODULES([GLIB], [glib-2.0 >= glib_version])
PKG_CHECK_MODULES([GIO], [
    gio-2.0 >= glib_version
    gio-unix-2.0 >= glib_version
])
PKG_CHECK_MODULES([GOBJECT], [gobject-2.0])
PKG_CHECK_MODULES([LIBXML], [libxml-2.0])
PKG_CHECK_MODULES([CURL], [libcurl])
PKG_CHECK_MODULES([SATYR], [satyr])
PKG_CHECK_MODULES([JOURNAL], [libsystemd])
PKG_CHECK_MODULES([AUGEAS], [augeas])
PKG_CHECK_MODULES([LIBARCHIVE], [libarchive])


AC_SEARCH_LIBS([forkpty], [util])
AC_REPLACE_FUNCS([forkpty])


AC_ARG_WITH(newt,
AS_HELP_STRING([--with-newt],[use newt (default is YES)]),
LIBREPORT_PARSE_WITH([newt]))
if test -z "$NO_NEWT"; then
AM_CONDITIONAL(BUILD_NEWT, true)
PKG_CHECK_MODULES([NEWT], [libnewt])
else
AM_CONDITIONAL(BUILD_NEWT, false)
fi dnl end NO_NEWT

AC_ARG_WITH(gtk,
AS_HELP_STRING([--with-gtk],[use gtk wizard and libreport-gtk (default is YES)]),
LIBREPORT_PARSE_WITH([gtk]))
if test -z "$NO_GTK"; then
AM_CONDITIONAL(BUILD_GTK, true)
PKG_CHECK_MODULES([GTK], [gtk+-3.0])
else
AM_CONDITIONAL(BUILD_GTK, false)
fi dnl end NO_GTK


AC_ARG_WITH(ureport,
AS_HELP_STRING([--with-ureport],[use uReport plugin (default is YES)]),
LIBREPORT_PARSE_WITH([ureport]))
if test -z "$NO_UREPORT"; then
AM_CONDITIONAL(BUILD_UREPORT, true)
PKG_CHECK_MODULES([JSON_C], [json], [
    JSON_C_PACKAGE=json
], [
    PKG_CHECK_MODULES([JSON_C], [json-c], [
        JSON_C_PACKAGE=json-c
    ])
])
else
AM_CONDITIONAL(BUILD_UREPORT, false)
JSON_C_PACKAGE=
fi dnl end NO_UREPORT

AC_SUBST([JSON_C_PACKAGE])

PKG_PROG_PKG_CONFIG

AC_CHECK_HEADER([archive.h], [],
   [AC_MSG_ERROR([archive.h is needed to build libreport])])

AC_CHECK_HEADERS([locale.h])

CONF_DIR='${sysconfdir}/${PACKAGE_NAME}'
DEFAULT_CONF_DIR='${datadir}/${PACKAGE_NAME}/conf.d'
VAR_RUN='${localstatedir}/run'
PLUGINS_CONF_DIR='${sysconfdir}/${PACKAGE_NAME}/plugins'
REPORT_PLUGINS_CONF_DIR='${sysconfdir}/libreport/plugins'
DEFAULT_REPORT_PLUGINS_CONF_DIR='${datadir}/${PACKAGE_NAME}/conf.d/plugins'
EVENTS_DIR='${pkgdatadir}/events'
EVENTS_DEFINITION_DIR='${sysconfdir}/${PACKAGE_NAME}/events.d'
EVENTS_CONF_DIR='${sysconfdir}/${PACKAGE_NAME}/events'
WORKFLOWS_DIR='${pkgdatadir}/workflows'
WORKFLOWS_DEFINITION_DIR='${sysconfdir}/${PACKAGE_NAME}/workflows.d'
PLUGINS_LIB_DIR='${libdir}/${PACKAGE_NAME}'
LIBEXEC_DIR='${libexecdir}'

DEBUG_DUMPS_DIR='${localstatedir}/spool/abrt'

AC_ARG_WITH(debugdumpsdir,
            [AS_HELP_STRING([--with-debugdumpdir=DIR],
                           [Directory where debugdumps are created])],
            [DEBUG_DUMPS_DIR="$withval"])

AC_ARG_WITH(largedatatmpdir,
            [AS_HELP_STRING([--with-largedatatmpdir=DIR],
                           [Directory where potentially large data are created (default: /var/tmp)])],
            [], [with_largedatatmpdir="/var/tmp"])
AC_SUBST([LARGE_DATA_TMP_DIR], [$with_largedatatmpdir])

AC_PATH_PROG(AUGPARSE, augparse, no)
[if test "$AUGPARSE" = "no"]
[then]
    [echo "The augparse program was not found in the search path. Please ensure"]
    [echo "that it is installed and its directory is included in the search path."]
    [echo "Then run configure again before attempting to build libreport."]
    [exit 1]
[fi]

AC_ARG_WITH([defaultdumpdirmode],
            AS_HELP_STRING([--with-defaultdumpdirmode=OCTAL-MODE],
                           [Default dump dir mode (default: 0x1A0 (0640))]),
            [], [with_defaultdumpdirmode="0x1A0"])
AC_SUBST([DEFAULT_DUMP_DIR_MODE], [$with_defaultdumpdirmode])

DUMP_DIR_OWNED_BY_USER=1
AC_ARG_ENABLE([userownsdumpdir],
              [AS_HELP_STRING([--enable-userownsdumpdir],[Configure abrt to be owner of dump directories (default: user owns dump dir)])],
              [DUMP_DIR_OWNED_BY_USER=0])


AC_ARG_ENABLE(doxygen-docs,
    AS_HELP_STRING([--enable-doxygen-docs],
	[build DOXYGEN documentation (requires Doxygen)]),
    [enable_doxygen_docs=$enableval],
    [enable_doxygen_docs=no]
)

# Doxygen Documentation

AC_PATH_PROG(DOXYGEN, doxygen, no)

AC_MSG_CHECKING([whether to build Doxygen documentation])

if test "$DOXYGEN" = "no" ; then
    have_doxygen=no
else
    have_doxygen=yes
fi

if test "$enable_doxygen_docs" = "yes" ; then
    if test "$have_doxygen" = "no"; then
        AC_MSG_ERROR([Building Doxygen docs explicitly required, but Doxygen not found])
    fi
    AC_MSG_RESULT(yes)
else
    AC_MSG_RESULT(no)
fi

AM_CONDITIONAL(DOXYGEN_DOCS_ENABLED, test "$enable_doxygen_docs" = "yes")

AC_SUBST(CONF_DIR)
AC_SUBST(DEFAULT_CONF_DIR)
AC_SUBST(VAR_RUN)
AC_SUBST(PLUGINS_CONF_DIR)
AC_SUBST(REPORT_PLUGINS_CONF_DIR)
AC_SUBST(DEFAULT_REPORT_PLUGINS_CONF_DIR)
AC_SUBST(EVENTS_DEFINITION_DIR)
AC_SUBST(EVENTS_CONF_DIR)
AC_SUBST(EVENTS_DIR)
AC_SUBST(PLUGINS_LIB_DIR)
AC_SUBST(DEBUG_DUMPS_DIR)
AC_SUBST(LIBEXEC_DIR)
AC_SUBST(WORKFLOWS_DIR)
AC_SUBST(WORKFLOWS_DEFINITION_DIR)
AC_SUBST(DUMP_DIR_OWNED_BY_USER)

# Initialize the test suite.
 AC_CONFIG_TESTDIR(tests)
 AC_CONFIG_FILES([tests/Makefile tests/atlocal])
 AM_MISSING_PROG([AUTOM4TE], [autom4te])
# Needed by tests/atlocal.in.
# CFLAGS may contain '-Werror=format-security'
AC_SUBST([O0CFLAGS], [`echo $CFLAGS   | sed 's/-O[[0-9]] *//' | sed 's/-Werror\(=[[^ ]]*\)\?//g'`])

AC_CONFIG_HEADERS([config.h])

AC_CHECK_PROGS([VALGRIND], [valgrind])

AC_CONFIG_FILES([
	Makefile
	apidoc/Makefile
	apidoc/Doxyfile
	apidoc/html/Makefile
	data/Makefile
	data/augeas/Makefile
	libreport.pc
	libreport-web.pc
	src/include/Makefile
	src/lib/Makefile
	src/report-python/Makefile
	src/report-python/report/Makefile
	src/Makefile
	src/gtk-helpers/Makefile
	src/gtk-helpers/libreport-gtk.pc
	src/gui-wizard-gtk/Makefile
	src/cli/Makefile
	src/report-newt/Makefile
	src/plugins/Makefile
	src/plugins/python/Makefile
	src/plugins/report_Bugzilla.xml.in
	src/client-python/Makefile
	src/client-python/reportclient/Makefile
	src/client-python/reportclient/internal/Makefile
	po/Makefile.in
	doc/Makefile
	src/workflows/Makefile
	tests/bugzilla_plugin.at
])

#	src/plugins/Makefile
#	src/cli/Makefile
#	po/Makefile.in
#	icons/Makefile
#])

AC_OUTPUT
