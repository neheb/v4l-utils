bin_PROGRAMS = v4l2-ctl
man_MANS = v4l2-ctl.1
DEFS :=

if HAVE_M32
if WITH_V4L2_COMPLIANCE_32
bin_PROGRAMS += v4l2-ctl-32
endif
endif

v4l2_ctl_SOURCES = v4l2-ctl.cpp v4l2-ctl.h v4l2-ctl-common.cpp v4l2-ctl-tuner.cpp \
	v4l2-ctl-io.cpp v4l2-ctl-stds.cpp v4l2-ctl-vidcap.cpp v4l2-ctl-vidout.cpp \
	v4l2-ctl-overlay.cpp v4l2-ctl-vbi.cpp v4l2-ctl-selection.cpp v4l2-ctl-misc.cpp \
	v4l2-ctl-streaming.cpp v4l2-ctl-sdr.cpp v4l2-ctl-edid.cpp v4l2-ctl-modes.cpp \
	v4l2-ctl-subdev.cpp v4l2-tpg-colors.c v4l2-tpg-core.c v4l-stream.c v4l2-ctl-meta.cpp \
	media-info.cpp v4l2-info.cpp codec-fwht.c codec-v4l2-fwht.c
v4l2_ctl_CPPFLAGS = -I$(top_srcdir)/utils/common

media-bus-format-names.h: $(top_srcdir)/include/linux/media-bus-format.h
	sed -e '/#define MEDIA_BUS_FMT/ ! d; s/.*FMT_//; /FIXED/ d; s/\t.*//; s/.*/{ \"&\", MEDIA_BUS_FMT_& },/;' \
	< $< > $@

BUILT_SOURCES = media-bus-format-names.h
CLEANFILES = $(BUILT_SOURCES)

if WITH_V4L2_CTL_LIBV4L
v4l2_ctl_LDADD = ../../lib/libv4l2/libv4l2.la ../../lib/libv4lconvert/libv4lconvert.la -lrt -lpthread
else
DEFS += -DNO_LIBV4L2
endif

if !WITH_V4L2_CTL_STREAM_TO
DEFS += -DNO_STREAM_TO
endif

v4l2-ctl-32$(EXEEXT): $(addprefix $(top_srcdir)/utils/v4l2-ctl/,$(v4l2_ctl_SOURCES)) media-bus-format-names.h
	cat $(addprefix $(top_srcdir)/utils/v4l2-ctl/,$(filter %.c,$(v4l2_ctl_SOURCES))) >$@.c
	$(COMPILE) -static -m32 -DNO_LIBV4L2 -c -I$(top_srcdir) -I$(top_srcdir)/include -I$(top_srcdir)/utils/common $@.c
	$(CXXCOMPILE) -static -m32 -DNO_LIBV4L2 -o $@ -I$(top_srcdir) -I$(top_srcdir)/include -I$(top_srcdir)/utils/common $(addprefix $(top_srcdir)/utils/v4l2-ctl/,$(filter %.cpp,$(v4l2_ctl_SOURCES))) $@.o
	rm -f $@.c $@.o

EXTRA_DIST = Android.mk v4l2-ctl.1
