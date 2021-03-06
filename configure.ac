# Inspired by work Copyright (C) 2006 Luca Filipozzi
# vim: set fdm=marker ts=2 sw=2 et:

AC_REVISION([$Id: configure.ac 1184 2011-08-17 22:29:06Z michael $])

AC_PREREQ(2.63)
AC_INIT([ircd-hybrid], [7.3.1], [bugs@ircd-hybrid.org])
AM_INIT_AUTOMAKE(1.11.1)
AM_MAINTAINER_MODE
AC_CONFIG_HEADER(config.h)
AC_CONFIG_SRCDIR(src/ircd.c)

# Checks for programs.
AC_PROG_CC_C99
AS_IF([test "$ac_cv_prog_cc_c99" = "no"],
    [AC_MSG_ERROR([no C99 compiler found. Aborting.])])
AC_PROG_YACC
AM_PROG_LEX
AC_PROG_INSTALL

# Initializing libtool.
LT_CONFIG_LTDL_DIR([libltdl])
LT_INIT([dlopen disable-static])
LTDL_INIT([recursive convenience])
LIBTOOL="$LIBTOOL --silent"

# Checks for libraries.
AX_CHECK_LIB_IPV4
AX_CHECK_LIB_IPV6

# Checks for typedefs, structures, and compiler characteristics.
AC_C_BIGENDIAN

# Checks for library functions.
AC_CHECK_FUNCS_ONCE(mmap     \
                    strtok_r \
                    usleep   \
                    strlcat  \
                    strlcpy  \
                    socketpair)

# Checks for header files.
AC_CHECK_HEADERS_ONCE(crypt.h        \
                      inttypes.h     \
                      stdint.h       \
                      sys/resource.h \
                      sys/param.h    \
                      errno.h        \
                      sys/syslog.h   \
                      types.h        \
                      socket.h       \
                      sys/wait.h     \
                      wait.h         \
                      link.h)

# check for /dev/null so we can use it to hold evil fd's
AC_MSG_CHECKING([for /dev/null])
AS_IF([test -c /dev/null], [
  AC_DEFINE(PATH_DEVNULL, "/dev/null",   [Path to /dev/null])
  AC_MSG_RESULT([yes])], [
  AC_DEFINE(PATH_DEVNULL, "devnull.log", [Path to /dev/null])
  AC_MSG_RESULT([no - using devnull.log])])


AC_SEARCH_LIBS(crypt, crypt)


AC_ARG_WITH(zlib-path,
AS_HELP_STRING([--with-zlib-path=DIR], [Path to libz.so for ziplinks support.]),
[LDFLAGS="-L$withval $LDFLAGS"],)

AC_ARG_ENABLE(zlib, AS_HELP_STRING([--disable-zlib],[Disable ziplinks support]),
[zlib=$enableval],[zlib=yes])

AS_IF([test "$zlib" = "yes"], [
  AC_CHECK_HEADER(zlib.h, [AC_CHECK_LIB(z, zlibVersion,
    [
      LIBS="-lz $LIBS"
      AC_DEFINE(HAVE_LIBZ, 1, [Define to 1 if zlib (-lz) is available.])
    ], zlib=no)
  ], zlib=no)
])

AC_ARG_WITH(libpcre-path,
AS_HELP_STRING([--with-libpcre-path=DIR], [Path to libpcre.so for PCRE support.]),
[LDFLAGS="-L$withval $LDFLAGS"],)

AC_ARG_ENABLE(libpcre, AS_HELP_STRING([--disable-libpcre],[Disable PCRE support]),
[libpcre=$enableval],[libpcre=yes])

AS_IF([test "$libpcre" = "yes"], [
  AC_CHECK_HEADER(pcre.h, [AC_CHECK_LIB(pcre, pcre_study,
    [
      LIBS="-lpcre $LIBS"
      AC_DEFINE(HAVE_LIBPCRE, 1, [Define to 1 if libpcre (-lpcre) is available.])
    ], libpcre=no)
  ], libpcre=no)
])
AM_CONDITIONAL(ENABLE_PCRE, [test "$libpcre" = yes])


dnl Openssl checks
AC_ARG_ENABLE(openssl,
[  --enable-openssl[=DIR]       Enable OpenSSL support (DIR optional).
  --disable-openssl            Disable OpenSSL support. ],
[ cf_enable_openssl=$enableval ],
[ cf_enable_openssl="auto" ])
AC_MSG_CHECKING([for OpenSSL])
if test "$cf_enable_openssl" != "no"; then
  cf_openssl_basedir=""
  if test "$cf_enable_openssl" != "auto" &&
     test "$cf_enable_openssl" != "yes"; then
     dnl Support for --enable-openssl=/some/place
     cf_openssl_basedir="${cf_enable_openssl}"
  else
    dnl Do the auto-probe here.  Check some common directory paths.
    for dirs in /usr/local/ssl /usr/pkg /usr/local /usr/lib /usr/lib/ssl\
                /opt /opt/openssl /usr/local/openssl; do
      if test -f "${dirs}/include/openssl/opensslv.h"; then
        cf_openssl_basedir="${dirs}"
        break
      fi
    done
    unset dirs
  fi

  dnl Now check cf_openssl_found to see if we found anything.
  if test ! -z "$cf_openssl_basedir"; then
    if test -f "${cf_openssl_basedir}/include/openssl/opensslv.h"; then
      CPPFLAGS="-I${cf_openssl_basedir}/include $CPPFLAGS"
      LDFLAGS="-L${cf_openssl_basedir}/lib $LDFLAGS"
    else
      dnl OpenSSL wasn't found in the directory specified.  Naughty
      dnl administrator...
      cf_openssl_basedir=""
    fi
  else
    dnl Check for stock FreeBSD 4.x and 5.x systems, since their files
    dnl are in /usr/include and /usr/lib.  In this case, we don't want to
    dnl change INCLUDES or LIBS, but still want to enable OpenSSL.
    dnl We can't do this check above, because some people want two versions
    dnl of OpenSSL installed (stock FreeBSD 4.x/5.x and /usr/local/ssl)
    dnl and they want /usr/local/ssl to have preference.
    if test -f "/usr/include/openssl/opensslv.h"; then
      cf_openssl_basedir="/usr"
    fi
  fi

  dnl If we have a basedir defined, then everything is okay.  Otherwise,
  dnl we have a problem.
  if test ! -z "$cf_openssl_basedir"; then
    LIBS="-lcrypto -lssl $LIBS"
    AC_MSG_RESULT([$cf_openssl_basedir])
    cf_enable_openssl="yes"
    AC_DEFINE(HAVE_LIBCRYPTO, 1, [If we support ssl])
  else
    AC_MSG_RESULT([not found. Please check your path.])
    cf_enable_openssl="no"
  fi
  unset cf_openssl_basedir
else
  dnl If --disable-openssl was specified
  AC_MSG_RESULT([disabled])
fi

AS_IF([test "$cf_enable_openssl" != "no"],
 [AC_MSG_CHECKING(for OpenSSL 0.9.7 or above)
  AC_RUN_IFELSE([
    AC_LANG_PROGRAM([
    #include <openssl/opensslv.h>
    #include <stdlib.h>],
    [[ exit(!(OPENSSL_VERSION_NUMBER >= 0x00907000)); ]])],
  [cf_openssl_version_ok=yes],
  [cf_openssl_version_ok=no],
  [cf_openssl_version_ok=no])

  AS_IF([test "$cf_openssl_version_ok" = "yes"],
    [AC_MSG_RESULT(found)

    AC_CHECK_LIB(crypto, RSA_free)
    AS_IF([test "$ac_cv_lib_crypto_RSA_free" = "yes"],
      [AC_CHECK_FUNCS(EVP_bf_cfb           \
                      EVP_cast5_cfb        \
                      EVP_idea_cfb         \
                      EVP_rc5_32_12_16_cfb \
                      EVP_des_ede3_cfb     \
                      EVP_des_cfb)])
    ],[AC_MSG_RESULT(no - OpenSSL support disabled)
    cf_enable_openssl="no"])])

if test "$cf_enable_openssl" != no; then
	dnl Check OpenSSL version (must be 0.9.6 or above!)
	AC_MSG_CHECKING(for OpenSSL 0.9.6 or above)
	AC_RUN_IFELSE(
		AC_LANG_PROGRAM(
	    	[#include <openssl/opensslv.h>
		#include <stdlib.h>],
		[[if (OPENSSL_VERSION_NUMBER >= 0x00906000)
		exit(0); else exit(1);]]),
	cf_openssl_version_ok=yes,
	cf_openssl_version_ok=no,
	cf_openssl_version_ok=no)

	if test "$cf_openssl_version_ok" = yes; then
		AC_MSG_RESULT(found)

		dnl Work around pmake/gmake conditional incompatibilities
		AC_SUBST(ENCSPEED, encspeed)

		dnl Do all the HAVE_LIBCRYPTO magic -- and check for ciphers
		CPPFLAGS="$CPPFLAGS $SSL_LIBS"
		AC_CHECK_LIB(crypto, RSA_free)
		if test "$ac_cv_lib_crypto_RSA_free" = yes; then
			LIBS="$LIBS -lcrypto"
			cf_openssl_ciphers=''
			AC_CHECK_FUNCS(EVP_bf_cfb,
			cf_openssl_ciphers="${cf_openssl_ciphers}BF/168 BF/128 ")
			AC_CHECK_FUNCS(EVP_cast5_cfb,
			cf_openssl_ciphers="${cf_openssl_ciphers}CAST/128 ")
			AC_CHECK_FUNCS(EVP_idea_cfb,
			cf_openssl_ciphers="${cf_openssl_ciphers}IDEA/128 ")
			AC_CHECK_FUNCS(EVP_rc5_32_12_16_cfb,
			cf_openssl_ciphers="${cf_openssl_ciphers}RC5.16/128 RC5.12/128 RC5.8/128 ")
			AC_CHECK_FUNCS(EVP_des_ede3_cfb,
			cf_openssl_ciphers="${cf_openssl_ciphers}3DES/168 ")
			AC_CHECK_FUNCS(EVP_des_cfb,
			cf_openssl_ciphers="${cf_openssl_ciphers}DES/56 ")
			AC_CHECK_FUNCS(EVP_aes_128_cfb,
			cf_openssl_ciphers="${cf_openssl_ciphers}AES/128 ")
			AC_CHECK_FUNCS(EVP_aes_256_cfb,
			cf_openssl_ciphers="${cf_openssl_ciphers}AES/256 ")
		fi
		SSL_LIBS="$SSL_LIBS -lssl -lcrypto"
		SSL_SRCS_ENABLE='$(SSL_SRCS)'
	else
	        AC_MSG_RESULT(no - OpenSSL support disabled)
	fi
	AC_CHECK_HEADER([openssl/sha.h], [
		AC_CHECK_FUNCS(SHA1_Init, AC_DEFINE(HAVE_OPENSSL_SHA, 1, [Define to 1 if SHA1 is available.]))
	])
fi

AM_CONDITIONAL(ENABLE_SSL, [test "$cf_enable_openssl" = yes])

AC_ARG_ENABLE(assert, AS_HELP_STRING([--enable-assert], 
                                     [Enable assert() statements]),
  [assert=$enableval], [assert=no])

AS_IF([test "$assert" = "no"],
  [AC_DEFINE(NDEBUG, 1, [Define to disable assert() statements.])])


AC_ARG_ENABLE(small-net, AS_HELP_STRING([--enable-small-net],
                                        [Enable small network support.]),
[small_net=$enableval], [small_net=no])

AS_IF([test "$small_net" = "yes"], [
  AC_DEFINE([NICKNAMEHISTORYLENGTH], 1500, [Size of the WHOWAS array.])
  AC_DEFINE([CHANNEL_HEAP_SIZE], 256, [Size of the channel heap.])
  AC_DEFINE([BAN_HEAP_SIZE], 128, [Size of the ban heap.])
  AC_DEFINE([CLIENT_HEAP_SIZE], 256, [Size of the client heap.])
  AC_DEFINE([LCLIENT_HEAP_SIZE], 128, [Size of the local client heap.])
  AC_DEFINE([DNODE_HEAP_SIZE], 256, [Size of the dlink_node heap.])
  AC_DEFINE([TOPIC_HEAP_SIZE], 256, [Size of the topic heap.])
  AC_DEFINE([DBUF_HEAP_SIZE], 64, [Size of the dbuf heap.])
  AC_DEFINE([AUTH_HEAP_SIZE], 128, [Size of the auth heap.])
  AC_DEFINE([DNS_HEAP_SIZE], 128, [Size of the dns heap.])], [

  AC_DEFINE([NICKNAMEHISTORYLENGTH], 15000, [Size of the WHOWAS array.])
  AC_DEFINE([CHANNEL_HEAP_SIZE], 1024, [Size of the channel heap.])
  AC_DEFINE([BAN_HEAP_SIZE], 1024, [Size of the ban heap.])
  AC_DEFINE([CLIENT_HEAP_SIZE], 1024, [Size of the client heap.])
  AC_DEFINE([LCLIENT_HEAP_SIZE], 512, [Size of the local client heap.])
  AC_DEFINE([DNODE_HEAP_SIZE], 1024, [Size of the dlink_node heap.])
  AC_DEFINE([TOPIC_HEAP_SIZE], 1024, [Size of the topic heap.])
  AC_DEFINE([DBUF_HEAP_SIZE], 512, [Size of the dbuf heap.])
  AC_DEFINE([AUTH_HEAP_SIZE], 512, [Size of the auth heap.])
  AC_DEFINE([DNS_HEAP_SIZE], 512, [Size of the dns heap.])])


# Argument processing.
AX_ARG_ENABLE_IOLOOP_MECHANISM
AX_ARG_WITH_NICKLEN
AX_ARG_WITH_TOPICLEN
AX_ARG_WITH_SYSLOG
AX_ARG_ENABLE_EFNET
AX_ARG_ENABLE_HALFOPS
AX_ARG_ENABLE_DEBUGGING
AX_ARG_ENABLE_WARNINGS
AX_ARG_ENABLE_SYSLOG

AC_DEFINE_DIR([PREFIX],[prefix],[Set to prefix.])
AC_DEFINE_DIR([SYSCONFDIR],[sysconfdir],[Set to sysconfdir.])
AC_DEFINE_DIR([LIBDIR],[libdir],[Set to libdir.])
AC_DEFINE_DIR([DATADIR],[datadir],[Set to datadir.])
AC_DEFINE_DIR([LOCALSTATEDIR],[localstatedir],[Set to localstatedir.])

AC_CONFIG_FILES(              \
       Makefile               \
       etc/Makefile           \
       servlink/Makefile      \
       contrib/Makefile       \
       contrib/help/Makefile  \
       src/Makefile           \
       libltdl/Makefile       \
       messages/Makefile      \
       modules/Makefile       \
       modules/core/Makefile  \
       doc/Makefile           \
       help/Makefile          \
       help/opers/Makefile    \
       help/users/Makefile    \
       tools/Makefile)

AC_OUTPUT
