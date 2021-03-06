libv4lconvert_sources = files(
    'bayer.c',
    'control/libv4lcontrol-priv.h',
    'control/libv4lcontrol.c',
    'control/libv4lcontrol.h',
    'cpia1.c',
    'crop.c',
    'flip.c',
    'helper-funcs.h',
    'hm12.c',
    'jidctflt.c',
    'jl2005bcd.c',
    'jpeg.c',
    'jpgl.c',
    'libv4lconvert-priv.h',
    'libv4lconvert.c',
    'libv4lsyscall-priv.h',
    'mr97310a.c',
    'pac207.c',
    'processing/autogain.c',
    'processing/gamma.c',
    'processing/libv4lprocessing-priv.h',
    'processing/libv4lprocessing.c',
    'processing/libv4lprocessing.h',
    'processing/whitebalance.c',
    'rgbyuv.c',
    'se401.c',
    'sn9c10x.c',
    'sn9c2028-decomp.c',
    'sn9c20x.c',
    'spca501.c',
    'spca561-decompress.c',
    'sq905c.c',
    'stv0680.c',
    'tinyjpeg-internal.h',
    'tinyjpeg.c',
    'tinyjpeg.h',
)

libv4lconvert_api = files(
    '../include/libv4lconvert.h',
)

install_headers(libv4lconvert_api)

libv4lconvert_deps = [
    dep_libm,
    dep_librt,
]

libv4lconvert_priv_libs = [
    '-lm',
    '-lrt',
]

libv4lconvert_c_args = []

if dep_jpeg.found()
    libv4lconvert_deps += dep_jpeg
    libv4lconvert_priv_libs += dep_jpeg_priv_libs
    libv4lconvert_sources += files(
        'jpeg_memsrcdest.c',
        'jpeg_memsrcdest.h'
    )
    libv4lconvert_c_args += [
        '-DHAVE_JPEG',
    ]
endif

if have_fork
    libv4lconvert_sources += files(
        'helper.c',
    )
    libv4lconvertprivdir = get_option('prefix') / get_option('libdir') / get_option('libv4lconvertsubdir')
    ov511_decomp_sources = files(
        'ov511-decomp.c',
    )
    ov511_decomp = executable('ov511-decomp',
                              ov511_decomp_sources,
                              install : true,
                              install_dir : libv4lconvertprivdir)
    ov518_decomp_sources = files(
        'ov518-decomp.c',
    )
    ov518_decomp = executable('ov518-decomp',
                              ov518_decomp_sources,
                              install : true,
                              install_dir : libv4lconvertprivdir)
endif

libv4lconvert = shared_library('v4lconvert',
                               libv4lconvert_sources,
                               install : true,
                               dependencies : libv4lconvert_deps,
                               c_args : libv4lconvert_c_args,
                               include_directories : v4l2_utils_incdir)

dep_libv4lconvert = declare_dependency(link_with : libv4lconvert)

pkg.generate(
    name : 'libv4lconvert',
    libraries : libv4lconvert,
    libraries_private : libv4lconvert_priv_libs,
    version : meson.project_version(),
    description : 'v4l format conversion library')
