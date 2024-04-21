noinst_PROGRAMS =	table-redis

table_redis_SOURCES =	table_redis.c dict.c log.c table_stdio.c util.c

LDADD =			$(LIBOBJS)

dist_man5_MANS =	table-redis.5

smtpdir =		${prefix}/libexec/smtpd

install-exec-local: $(noinst_PROGRAMS)
	$(MKDIR_P) $(DESTDIR)$(smtpdir)
	$(INSTALL_PROGRAM_ENV) $(INSTALL_PROGRAM) $(noinst_PROGRAMS) $(DESTDIR)$(smtpdir)

README.md: table-redis.5
	mandoc -Tmarkdown -l table-redis.5 > README.md
