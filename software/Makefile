PROJM =  chan_dongle.so
PROJS =  chan_dongles.so

chan_donglem_so_OBJS =  app.o at_command.o at_parse.o at_queue.o at_read.o at_response.o \
	chan_dongle.o channel.o char_conv.o cli.o helpers.o manager.o \
	memmem.o ringbuffer.o cpvt.o dc_config.o pdu.o mixbuffer.o pdiscovery.o

chan_dongles_so_OBJS = single.o

test1_OBJS = test/test1.o ringbuffer.o mixbuffer.o
parse_OBJS = test/parse.o at_parse.o char_conv.o pdu.o
discovery_OBJS = tools/discovery.o tools/tty.o

SOURCES = app.c at_command.c at_parse.c at_queue.c at_read.c at_response.c \
	chan_dongle.c channel.c char_conv.c cli.c cpvt.c dc_config.c helpers.c \
	manager.c memmem.c ringbuffer.c single.c pdu.c mixbuffer.c pdiscovery.c

test_SOURCES = test/test1.c test/parse.c
tools_SOURCES = tools/discovery.c tools/tty.c

HEADERS = app.h at_command.h at_parse.h at_queue.h at_read.h at_response.h \
	chan_dongle.h channel.h char_conv.h cli.h cpvt.h dc_config.h export.h \
	helpers.h manager.h memmem.h ringbuffer.h pdu.h mixbuffer.h pdiscovery.h \
	mutils.h

tools_HEADERS = tools/tty.h

EXTRA_DIST = BUGS COPYRIGHT.txt LICENSE.txt README.txt TODO.txt INSTALL \
	Makefile.in config.h.in configure.ac stamp-h.in etc contrib

BUILD_TOOLS = configure config.sub install-sh missing config.guess

CC = gcc
LD = gcc
STRIP = strip
RM = rm -fr
INSTALL = /usr/bin/install -c
CHMOD = chmod

# -DAST_MODULE=\"$(PROJM)\" -D_THREAD_SAFE
CFLAGS  = -g -O2 -O6 -Wno-old-style-declaration -I$(srcdir) -DAST_MODULE_SELF_SYM=__internal_chan_dongle_self \
	 -D_GNU_SOURCE -I/usr/include -I/usr/include -DHAVE_CONFIG_H  -fvisibility=hidden -fPIC -Wall -Wextra -MD -MT $@ -MF .$(subst /,_,$@).d -MP
LDFLAGS =   
SOLINK  = -shared -Xlinker -x
LIBS    = 
DISTNAME= chan_dongle-1.1.r43gh=47cg

srcdir = .


all: chan_dongle.so

install: all
	$(STRIP) $(PROJM)
	$(INSTALL) -m 755 $(PROJM) /usr/lib/asterisk/modules

$(PROJM): $(chan_donglem_so_OBJS) Makefile
	$(LD) $(LDFLAGS) $(SOLINK) -o $@ $(chan_donglem_so_OBJS) $(LIBS)

$(PROJS): $(chan_dongles_so_OBJS) Makefile
	$(LD) $(LDFLAGS) $(SOLINK) -o $@ $(chan_dongles_so_OBJS) $(LIBS)
	$(CHMOD) 755 $@
	mv $@ chan_dongle.so

.c.o: Makefile config.h
	$(CC) $(CFLAGS) $(MAKE_DEPS) -o $@ -c $<

check: tests
	./test/test1
	./test/parse

tests: test/test1 test/parse

test/test1: $(test1_OBJS)
	$(LD) $(LDFLAGS) -o $@ $(test1_OBJS) $(LIBS)

test/parse: $(parse_OBJS)
	$(LD) $(LDFLAGS) -o $@ $(parse_OBJS) $(LIBS)

tools: tools/discovery

tools/discovery: $(discovery_OBJS)
	$(LD) $(LDFLAGS) -o $@ $(discovery_OBJS) $(LIBS)

clean:
	$(RM) $(PROJM) $(PROJS) *.o *.core .*.d autom4te.cache test/test1 test/*.o tools/discovery test/*.o

distclean: clean
	$(RM) Makefile aclocal.m4 compile \
		config.guess config.h config.log config.status config.sub \
		configure install-sh missing stamp-h stamp-h1

dist: $(SOURCES) $(HEADERS) $(EXTRA_DIST) $(BUILD_TOOLS)
	@mkdir $(DISTNAME) $(DISTNAME)/test $(DISTNAME)/tools
	@cp -a $(SOURCES) $(HEADERS) $(EXTRA_DIST) $(BUILD_TOOLS) $(DISTNAME)
	@cp -a $(test_SOURCES) $(DISTNAME)/test
	@cp -a $(tools_SOURCES) $(tools_HEADERS) $(DISTNAME)/tools
	tar czf $(DISTNAME).tgz $(DISTNAME) --exclude .svn -h
	@$(RM) $(DISTNAME)

configure: configure.ac
	autoconf

config.h: stamp-h
stamp-h: config.h.in config.status
	./config.status

Makefile: Makefile.in config.status
	./config.status

config.status: configure
	./config.status --recheck

ifneq ($(wildcard .*.d),)
   include .*.d
endif
