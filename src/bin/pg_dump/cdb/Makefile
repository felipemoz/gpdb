#-------------------------------------------------------------------------
#
# Makefile for src/bin/pg_dump/cdb
#
# Portions Copyright (c) 1996-2002, PostgreSQL Global Development Group
# Portions Copyright (c) 1994, Regents of the University of California
#
# src/bin/pg_dump/cdb/Makefile
#
#-------------------------------------------------------------------------

subdir = src/bin/pg_dump/cdb
top_builddir = ../../../..
include $(top_builddir)/src/Makefile.global

ifneq ($(PORTNAME), win32)
override CFLAGS += $(PTHREAD_CFLAGS) -pthread -Wno-error
endif


# The frontend doesn't need everything that's in LIBS, some are backend only
LIBS := $(filter-out -lresolv, $(LIBS))
# This program isn't interactive, so doesn't need these
LIBS := $(filter-out -lreadline -ledit -ltermcap -lncurses -lcurses -lcurl -lssl -lcrypto, $(LIBS))

ifeq ($(enable_ddboost), yes)
DDBOOSTLIB += -lDDBoost
endif

ifeq ($(enable_netbackup), yes)
NETBACKUPLIB76 += -L../../../../gpAux/ext/$(BLD_ARCH)/Netbackup/nbu76/lib -lxbsa64
NETBACKUPLIB75 += -L../../../../gpAux/ext/$(BLD_ARCH)/Netbackup/nbu75/lib -lxbsa64 -lnbclientcST -lnbbasecST -lvxcPBXST
NETBACKUPLIB71 += -L../../../../gpAux/ext/$(BLD_ARCH)/Netbackup/nbu71/lib -lxbsa64 -lnbclientcST -lnbbasecST -lvxcPBXST
GPBSALIB += -L$(top_builddir)/src/bin/pg_dump/cdb -lgpbsa -Wl,-rpath-link,'../../../../gpAux/ext/$(BLD_ARCH)/Netbackup/nbu76/lib'
override CFLAGS += -I$(top_builddir)/gpAux/ext/$(BLD_ARCH)/Netbackup/nbu76/include
override CPPFLAGS += -I$(top_builddir)/gpAux/ext/$(BLD_ARCH)/Netbackup/nbu76/include
endif

PGDUMP_SRCDIR= $(top_srcdir)/src/bin/pg_dump
PGDUMP_DIR= $(top_builddir)/src/bin/pg_dump

override CPPFLAGS := -I$(PGDUMP_SRCDIR) -I$(libpq_srcdir) $(CPPFLAGS) -DBINDIR=\"$(bindir)\"
override CFLAGS += -I$(top_builddir)/src/include -I$(top_builddir)/src/interfaces/libpq -I$(top_builddir)/gpAux/ext/$(BLD_ARCH)/include


OBJS= $(PGDUMP_DIR)/pg_backup_db.o $(PGDUMP_DIR)/pg_backup_custom.o \
	$(PGDUMP_DIR)/pg_backup_files.o $(PGDUMP_DIR)/pg_backup_null.o \
	$(PGDUMP_DIR)/pg_backup_tar.o $(PGDUMP_DIR)/dumputils.o


KEYWRDOBJS = ../keywords.o ../kwlookup.o

all: submake-libpq submake-libpgport cdb_dump cdb_dump_agent cdb_restore cdb_restore_agent gpddboost cdb_bsa_dump_agent cdb_bsa_restore_agent cdb_bsa_query_agent cdb_bsa_delete_agent libgpbsa.so libgpbsa76.so libgpbsa75.so libgpbsa71.so

ifeq ($(enable_netbackup), yes)
cdb_bsa_dump_agent cdb_bsa_restore_agent cdb_bsa_query_agent cdb_bsa_delete_agent: libgpbsa.so
endif

cdb_dump: cdb_dump.o cdb_backup_status.o cdb_seginst.o cdb_backup_state.o cdb_table.o cdb_backup_archiver.o cdb_dump_util.o cdb_dump_include.o cdb_lockbox.o $(PGDUMP_DIR)/common.o $(OBJS) $(KEYWRDOBJS) $(libpq_builddir)/libpq.a $(EXTRA_OBJS)
	$(CC) $(CFLAGS) cdb_dump.o cdb_backup_status.o cdb_seginst.o cdb_backup_state.o cdb_table.o cdb_backup_archiver.o cdb_dump_util.o cdb_dump_include.o cdb_lockbox.o $(PGDUMP_DIR)/common.o $(OBJS) $(KEYWRDOBJS)  $(libpq) $(LDFLAGS) $(DDBOOSTLIB) $(LIBS) -o $@

cdb_dump_agent: cdb_dump_agent.o $(PGDUMP_DIR)/common.o $(PGDUMP_DIR)/pg_dump_sort.o cdb_backup_archiver.o cdb_dump_util.o cdb_seginst.o cdb_table.o cdb_backup_status.o cdb_backup_state.o cdb_dump_include.o cdb_lockbox.o $(OBJS) $(KEYWRDOBJS) $(libpq_builddir)/libpq.a
	$(CC) $(CFLAGS) cdb_dump_agent.o $(PGDUMP_DIR)/common.o $(PGDUMP_DIR)/pg_dump_sort.o cdb_dump_util.o cdb_seginst.o cdb_table.o cdb_backup_archiver.o cdb_backup_status.o cdb_backup_state.o cdb_dump_include.o cdb_lockbox.o $(OBJS) $(KEYWRDOBJS)  $(libpq) $(LDFLAGS) $(DDBOOSTLIB) $(LIBS) -o $@

#cdb_dumpall_agent: cdb_dumpall_agent.o ../common.o cdb_backup_archiver.o cdb_dump_util.o cdb_lockbox.o cdb_seginst.o cdb_table.o cdb_backup_status.o $(OBJS) $(KEYWRDOBJS) $(libpq_builddir)/libpq.a
#	$(CC) $(CFLAGS) cdb_dumpall_agent.o cdb_backup_archiver.o $(OBJS) $(KEYWRDOBJS)  $(libpq) $(LDFLAGS) $(LIBS) -o $@

cdb_restore: cdb_restore.o cdb_backup_status.o cdb_seginst.o cdb_backup_state.o cdb_table.o cdb_dump_util.o cdb_lockbox.o $(PGDUMP_DIR)/dumputils.o $(libpq_builddir)/libpq.a
	$(CC) $(CFLAGS) cdb_restore.o cdb_backup_status.o cdb_seginst.o cdb_backup_state.o cdb_table.o cdb_dump_util.o cdb_lockbox.o $(KEYWRDOBJS) $(PGDUMP_DIR)/dumputils.o $(libpq) $(LDFLAGS) $(DDBOOSTLIB) $(LIBS) -o $@

cdb_restore_agent: cdb_restore_agent.o cdb_backup_archiver.o cdb_backup_status.o cdb_dump_util.o cdb_seginst.o cdb_table.o cdb_lockbox.o $(OBJS) $(libpq_builddir)/libpq.a
	$(CC) $(CFLAGS) cdb_restore_agent.o  cdb_backup_archiver.o  cdb_backup_status.o cdb_dump_util.o cdb_seginst.o cdb_table.o cdb_lockbox.o $(OBJS) $(KEYWRDOBJS)  $(libpq) $(LDFLAGS) $(DDBOOSTLIB) $(LIBS) -o $@

gpddboost: cdb_ddboost_util.o cdb_backup_status.o cdb_seginst.o cdb_backup_state.o cdb_table.o cdb_dump_util.o cdb_lockbox.o $(PGDUMP_DIR)/dumputils.o $(libpq_builddir)/libpq.a
	$(CC) $(CFLAGS) cdb_ddboost_util.o cdb_backup_status.o cdb_seginst.o cdb_backup_state.o cdb_table.o cdb_dump_util.o cdb_lockbox.o $(PGDUMP_DIR)/dumputils.o $(KEYWRDOBJS) $(libpq) $(LDFLAGS) $(DDBOOSTLIB) $(LIBS) -o $@

libgpbsa76.so: cdb_bsa_util.c $(libpq_builddir)/libpq.a
	$(CC) $(CFLAGS_SL) $(CPPFLAGS) cdb_bsa_util.c $(libpq) $(LDFLAGS) -shared $(DDBOOSTLIB) $(NETBACKUPLIB76) $(LIBS) -o $@

libgpbsa75.so: cdb_bsa_util.c $(libpq_builddir)/libpq.a
	$(CC) $(CFLAGS_SL) $(CPPFLAGS) cdb_bsa_util.c $(libpq) $(LDFLAGS) -shared $(DDBOOSTLIB) $(NETBACKUPLIB75) $(LIBS) -o $@

libgpbsa71.so: cdb_bsa_util.o $(libpq_builddir)/libpq.a
	$(CC) $(CFLAGS_SL) $(CPPFLAGS) cdb_bsa_util.c $(libpq) $(LDFLAGS) -shared $(DDBOOSTLIB) $(NETBACKUPLIB71) $(LIBS) -o $@

libgpbsa.so: libgpbsa76.so
	cp libgpbsa76.so libgpbsa.so

cdb_bsa_dump_agent cdb_bsa_restore_agent cdb_bsa_query_agent cdb_bsa_delete_agent: cdb_dump_util.o cdb_lockbox.o $(libpq_builddir)/libpq.a

cdb_bsa_dump_agent: cdb_bsa_dump_agent.o
	$(CC) $(CFLAGS) cdb_bsa_dump_agent.o cdb_dump_util.o cdb_lockbox.o $(PGDUMP_DIR)/dumputils.o $(KEYWRDOBJS) $(libpq) $(LDFLAGS) $(DDBOOSTLIB) $(GPBSALIB) $(LIBS) -o $@

cdb_bsa_restore_agent: cdb_bsa_restore_agent.o
	$(CC) $(CFLAGS) cdb_bsa_restore_agent.o cdb_dump_util.o cdb_lockbox.o $(PGDUMP_DIR)/dumputils.o $(KEYWRDOBJS) $(libpq) $(LDFLAGS) $(DDBOOSTLIB) $(GPBSALIB) $(LIBS) -o $@

cdb_bsa_query_agent: cdb_bsa_query_agent.o
	$(CC) $(CFLAGS) cdb_bsa_query_agent.o cdb_dump_util.o cdb_lockbox.o $(PGDUMP_DIR)/dumputils.o $(KEYWRDOBJS) $(libpq) $(LDFLAGS) $(DDBOOSTLIB) $(GPBSALIB) $(LIBS) -o $@

cdb_bsa_delete_agent: cdb_bsa_delete_agent.o
	$(CC) $(CFLAGS) cdb_bsa_delete_agent.o cdb_dump_util.o cdb_lockbox.o $(PGDUMP_DIR)/dumputils.o $(KEYWRDOBJS) $(libpq) $(LDFLAGS) $(DDBOOSTLIB) $(GPBSALIB) $(LIBS) -o $@

.PHONY: submake-backend
submake-backend:
	$(MAKE) -C $(top_builddir)/src/backend/parser keywords.o

install: all installdirs
	$(INSTALL_PROGRAM) cdb_dump$(X) $(DESTDIR)$(bindir)/gp_dump$(X)
	$(INSTALL_PROGRAM) cdb_restore$(X) $(DESTDIR)$(bindir)/gp_restore$(X)
	$(INSTALL_PROGRAM) cdb_dump_agent$(X) $(DESTDIR)$(bindir)/gp_dump_agent$(X)
	$(INSTALL_PROGRAM) cdb_restore_agent$(X) $(DESTDIR)$(bindir)/gp_restore_agent$(X)
	$(INSTALL_PROGRAM) gpddboost$(X) $(DESTDIR)$(bindir)/gpddboost$(X)
	$(INSTALL_PROGRAM) libgpbsa76.so$(X) $(DESTDIR)$(libdir)/nbu76/lib/libgpbsa.so$(X)
	$(INSTALL_PROGRAM) libgpbsa75.so$(X) $(DESTDIR)$(libdir)/nbu75/lib/libgpbsa.so$(X)
	$(INSTALL_PROGRAM) libgpbsa71.so$(X) $(DESTDIR)$(libdir)/nbu71/lib/libgpbsa.so$(X)
	$(INSTALL_PROGRAM) cdb_bsa_dump_agent$(X) $(DESTDIR)$(bindir)/gp_bsa_dump_agent$(X)
	$(INSTALL_PROGRAM) cdb_bsa_restore_agent$(X) $(DESTDIR)$(bindir)/gp_bsa_restore_agent$(X)
	$(INSTALL_PROGRAM) cdb_bsa_query_agent$(X) $(DESTDIR)$(bindir)/gp_bsa_query_agent$(X)
	$(INSTALL_PROGRAM) cdb_bsa_delete_agent$(X) $(DESTDIR)$(bindir)/gp_bsa_delete_agent$(X)

installdirs:
	$(MKDIR_P) $(DESTDIR)$(bindir)

uninstall:
	rm -f $(addprefix $(DESTDIR)$(bindir)/, cdb_dump$(X) cdb_dump_agent$(X) cdb_restore$(X) cdb_restore_agent$(X) cdb_bsa_dump_agent$(X)  cdb_bsa_restore_agent$(X) cdb_bsa_query_agent$(X) cdb_bsa_delete_agent$(X))

clean distclean maintainer-clean:
	rm -f cdb_dump$(X) cdb_restore$(X) cdb_dump_agent$(X) cdb_restore_agent$(X) cdbheadsync$(X) cdbheadmakeactive$(X) cdb_bsa_dump_agent$(X) cdb_bsa_restore_agent$(X) \
	cdb_bsa_query_agent$(X) cdb_bsa_delete_agent$(X) $(OBJS) $(OBJS) cdb_dump.o cdb_restore.o cdb_dump_agent.o cdb_restore_agent.o $(PGDUMP_DIR)/common.o cdb_backup_status.o cdb_dump_include.o cdb_seginst.o \
	cdb_backup_state.o cdb_table.o cdb_dump_util.o cdb_backup_archiver.o cdbheadsync.o cdbheadmakeactive.o kwlookup.c cdb_ddboost_util.o cdb_bsa_dump_agent.o cdb_bsa_query_agent.o \
	cdb_bsa_restore_agent.o cdb_bsa_delete_agent.o libgpbsa*.so $(KEYWRDOBJS)
	$(MAKE) -C test clean
