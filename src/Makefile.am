AUTOMAKE_OPTIONS = foreign

sbin_PROGRAMS = ircd

AM_YFLAGS = -d

AM_CPPFLAGS = $(LTDLINCL) -I$(top_srcdir)/include
ircd_LDFLAGS = -export-dynamic
ircd_LDADD = $(LIBLTDL)
ircd_DEPENDENCIES = $(LTDLDEPS)

ircd_SOURCES = balloc.c          \
               channel.c         \
               channel_mode.c    \
               client.c          \
               csvlib.c          \
               dbuf.c            \
               dynlink.c         \
               event.c           \
               fdlist.c          \
               fileio.c          \
               getopt.c          \
               hash.c            \
               hook.c            \
               hostmask.c        \
               irc_res.c         \
               irc_reslib.c      \
               irc_string.c      \
               ircd.c            \
               ircd_signal.c     \
               list.c            \
               listener.c        \
               match.c           \
               memory.c          \
               modules.c         \
               motd.c            \
               rng_mt.c          \
               numeric.c         \
               packet.c          \
               parse.c           \
               s_conf.c          \
               s_bsd_epoll.c     \
               s_bsd_poll.c      \
               s_bsd_sigio.c     \
               s_bsd_devpoll.c   \
               s_bsd_kqueue.c    \
               s_bsd_select.c    \
               restart.c         \
               resv.c            \
               s_auth.c          \
               s_bsd.c           \
               s_gline.c         \
               s_log.c           \
               s_misc.c          \
               s_serv.c          \
               s_user.c          \
               send.c            \
               sprintf_irc.c     \
               version.c         \
               watch.c           \
               whowas.c          \
               ircd_parser.y     \
               ircd_lexer.l
if ENABLE_SSL
ircd_SOURCES += rsa.c
endif
