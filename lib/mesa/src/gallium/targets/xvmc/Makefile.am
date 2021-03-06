include $(top_srcdir)/src/gallium/Automake.inc

AM_CFLAGS = \
	$(GALLIUM_TARGET_CFLAGS)

xvmcdir = $(XVMC_LIB_INSTALL_DIR)
xvmc_LTLIBRARIES = libXvMCgallium.la

nodist_EXTRA_libXvMCgallium_la_SOURCES = dummy.cpp
libXvMCgallium_la_SOURCES =

libXvMCgallium_la_LDFLAGS = \
	-shared \
	-module \
	-no-undefined \
	-version-number $(XVMC_MAJOR):$(XVMC_MINOR) \
	$(GC_SECTIONS) \
	$(LD_NO_UNDEFINED)

if HAVE_LD_VERSION_SCRIPT
libXvMCgallium_la_LDFLAGS += \
	-Wl,--version-script=$(top_srcdir)/src/gallium/targets/xvmc/xvmc.sym
endif # HAVE_LD_VERSION_SCRIPT

libXvMCgallium_la_LIBADD = \
	$(top_builddir)/src/gallium/state_trackers/xvmc/libxvmctracker.la \
	$(top_builddir)/src/gallium/auxiliary/libgalliumvlwinsys.la \
	$(top_builddir)/src/gallium/auxiliary/libgalliumvl.la \
	$(top_builddir)/src/gallium/auxiliary/libgallium.la \
	$(top_builddir)/src/util/libmesautil.la \
	$(XVMC_LIBS) \
	$(VL_LIBS) \
	$(XCB_DRI3_LIBS) \
	$(LIBDRM_LIBS) \
	$(GALLIUM_COMMON_LIB_DEPS)

EXTRA_libXvMCgallium_la_DEPENDENCIES = xvmc.sym
EXTRA_DIST = xvmc.sym meson.build

TARGET_DRIVERS =
TARGET_CPPFLAGS =
TARGET_LIB_DEPS =

include $(top_srcdir)/src/gallium/drivers/nouveau/Automake.inc

include $(top_srcdir)/src/gallium/drivers/r600/Automake.inc

if HAVE_GALLIUM_STATIC_TARGETS

libXvMCgallium_la_SOURCES += target.c
libXvMCgallium_la_CPPFLAGS = $(TARGET_CPPFLAGS)
libXvMCgallium_la_LIBADD += \
	$(top_builddir)/src/gallium/auxiliary/pipe-loader/libpipe_loader_static.la \
	$(GALLIUM_PIPE_LOADER_WINSYS_LIBS) \
	$(TARGET_LIB_DEPS) \
	$(TARGET_COMPILER_LIB_DEPS) \
	$(TARGET_RADEON_WINSYS) $(TARGET_RADEON_COMMON)

else # HAVE_GALLIUM_STATIC_TARGETS
libXvMCgallium_la_LIBADD += \
	$(top_builddir)/src/gallium/auxiliary/pipe-loader/libpipe_loader_dynamic.la

endif # HAVE_GALLIUM_STATIC_TARGETS

if HAVE_GALLIUM_LLVM
libXvMCgallium_la_LIBADD += $(LLVM_LIBS)
libXvMCgallium_la_LDFLAGS += $(LLVM_LDFLAGS)
endif

# hardlink each megadriver instance, but don't actually have
# libXvMCgallium.so in the set of final installed files.
install-data-hook:
	$(AM_V_GEN)dest_dir=$(DESTDIR)/$(xvmcdir);			\
	for i in $(TARGET_DRIVERS); do					\
		j=libXvMCgallium.$(LIB_EXT);				\
		k=libXvMC$${i}.$(LIB_EXT);				\
		l=$${k}.$(XVMC_MAJOR).$(XVMC_MINOR);			\
		ln -f $${dest_dir}/$${j}.$(XVMC_MAJOR).$(XVMC_MINOR)	\
		      $${dest_dir}/$${l};				\
	done;								\
	$(RM) $${dest_dir}/libXvMCgallium.*;				\
	$(RM) -d $${dest_dir} &>/dev/null || true

uninstall-hook:
	for i in $(TARGET_DRIVERS); do					\
		$(RM) $(DESTDIR)$(xvmcdir)/libXvMC$${i}.so{,.$(XVMC_MAJOR){,.$(XVMC_MINOR){,.0}}}; \
	done;
