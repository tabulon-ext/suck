dnl Process this file with autoconf to produce a configure script.
dnl Autoconfigure input file for suck

AC_INIT(suck.c)
AC_CONFIG_HEADER(config.h)

AC_ARG_WITH(inn-lib, [ --with-inn-lib=path location of libinn.a])
AC_ARG_WITH(inn-include, [ --with-inn-include=path location of inn include files])
AC_ARG_WITH(perl-exe, [ --with-perl-exe=full path to perl executable eg: /usr/bin])

# we want these before the checks, so the checks can modify their values
test -z "$CFLAGS" && CFLAGS=-O2 AC_SUBST(CFLAGS)
test -z "$LDFLAGS" && LDFLAGS=-s AC_SUBST(LDFLAGS)

AC_PROG_CC
AC_PROG_INSTALL
AC_PROG_MAKE_SET
AC_SUBST(GCC)

dnl Checks for libraries.
AC_CHECK_LIB(nsl, gethostbyname)
AC_CHECK_LIB(socket, socket)
AC_CHECK_LIB(socks, Rconnect)
AC_CHECK_LIB(socks5, SOCKSconnect)
AC_CHECK_LIB(cV, fprintf)
dnl Do I need -lcrypto????? for SSL
AC_CHECK_LIB(ssl, SSL_get_error, [SSL_LIB="-lssl"; SSL_DEFS="-DHAVE_LIBSSL"])

dnl Checks for header files.
AC_HEADER_DIRENT
AC_HEADER_STDC
AC_HEADER_SYS_WAIT
AC_CHECK_HEADERS(fcntl.h sys/time.h unistd.h limits.h regex.h sys/select.h net/socket.h arpa/inet.h socks.h)

dnl Checks for typedefs, structures, and compiler characteristics.
AC_C_CONST
AC_TYPE_PID_T
AC_TYPE_SIZE_T
AC_HEADER_TIME

dnl Checks for library functions.
AC_TYPE_SIGNAL
AC_CHECK_FUNCS(gettimeofday select strerror memmove setvbuf sigaction)
if test "x$with_inn_lib" != "xno" ; then
    LDFLAGS="${LDFLAGS} $DB_LIB -L${with_inn_lib}"
fi
if test "x$with_inn_include" != "xno" ; then
   testpath=$with_inn_include
else
   testpath="/usr/include/inn /usr/local/include /usr/local/include/inn"
fi
HISTORY="chkhistory_db.o"
AC_CHECK_LIB(inn, main, [
  DB_LIB="-linn"
  DB_TYPE="-DUSE_INN23"
  savedLIBS=$LIBS
  LIBS="$LIBS -linn"
  AC_CHECK_FUNC(QIOopen, , [
    LIBS="$savedLIBS -lstorage"
    AC_CHECK_FUNC(QIOopen, [DB_LIB="$DB_LIB -lstorage"])
  ])
  LIBS=$savedLIBS
  AC_MSG_CHECKING([for libinn.h])
  for path in $testpath ; do
    if [ test -f $path/libinn.h ] || [ test -f $path/inn/libinn.h ] ; then
      AC_MSG_RESULT($path)
      found="yes"
      CPPFLAGS="$CPPFLAGS -I$path"
      break
    fi
  done
  test "$found" != "yes" && AC_MSG_RESULT(not found)
  ], [
  AC_CHECK_LIB(dbz, main, [DB_LIB="-ldbz"; DB_TYPE="-DUSE_DBZ"], [
    AC_CHECK_LIB(ndbm, main, [DB_LIB="-lndbm"; DB_TYPE="-DUSE_NDBM"], [
      AC_CHECK_LIB(gdbm, main, [DB_LIB="-lgdbm"; DB_TYPE="-DUSE_GDBM"], [
        AC_CHECK_LIB(dbm, main, [DB_LIB="-ldbm"; DB_TYPE="-DUSE_DBM"], [
         HISTORY="chkhistory.o"])])])])
])

if test "$with_perl_exe" ; then
	AC_CHECK_PROG(PERL, perl, true, false, [$with_perl_exe])
	whichperl=${with_perl_exe}/perl
else
	AC_CHECK_PROG(PERL, perl, true, false)
	whichperl=perl
fi
if test "$PERL" = "true"; then
  found="no"
  AC_MSG_CHECKING([for libperl.a])
  for path in `$whichperl -e 'foreach $i (@INC) { printf("%s ", $i) }'`; do
    if test -f $path/libperl.a; then
      AC_MSG_RESULT($path)
      found="yes"
      break
    fi
    path="${path}/CORE"
    if test -f $path/libperl.a; then
      AC_MSG_RESULT($path)
      found="yes"
      break
    fi
  done
  if test "$found" = "yes"; then
    PERL_DEFS="-DPERL_EMBED -Dbool=char -DHAS_BOOL"
    PERL_LIB="-lperl"
    PERL_LIB_LOC="-L$path"
    PERL_INC_LOC="-I$path"
    AC_CHECK_LIB(m, cos, [PERL_LIB="$PERL_LIB -lm"])
    AC_CHECK_LIB(crypt, crypt, [PERL_LIB="$PERL_LIB -lcrypt"]) 
  else
     AC_MSG_RESULT(not found)
  fi
fi

#AC_OUTPUT(Spanish.docs/Makefile Makefile, echo timestamp > stamp-h)
AC_SUBST(DB_TYPE)
AC_SUBST(DB_LIB)
AC_SUBST(HISTORY)
AC_SUBST(SSL_LIB)
AC_SUBST(SSL_DEFS)
AC_SUBST(PERL_DEFS)
AC_SUBST(PERL_LIB)
AC_SUBST(PERL_LIB_LOC)
AC_SUBST(PERL_INC_LOC)

AC_OUTPUT(Makefile, echo timestamp > stamp-h)
