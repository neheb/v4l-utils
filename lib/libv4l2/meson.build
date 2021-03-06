libv4l2_sources = files(
    'libv4l2-priv.h',
    'libv4l2.c',
    'log.c',
)

libv4l2_api = files(
    '../include/libv4l-plugin.h',
    '../include/libv4l2.h',
)

install_headers(libv4l2_api)

libv4l2_deps = [
    dep_libdl,
    dep_libv4lconvert,
    dep_threads,
]

libv4l2_c_args = []

if get_option('v4l-plugins')
    libv4l2_sources += files('v4l2-plugin.c')
    libv4l2_c_args += [
        '-DHAVE_V4L_PLUGINS',
        '-DLIBV4L2_PLUGIN_DIR="@0@"'.format(libv4l2plugindir)
    ]
endif

libv4l2 = shared_library('v4l2',
                         libv4l2_sources,
                         install : true,
                         dependencies : libv4l2_deps,
                         c_args : libv4l2_c_args,
                         include_directories : v4l2_utils_incdir)

dep_libv4l2 = declare_dependency(link_with : libv4l2)

pkg.generate(
    name : 'libv4l2',
    libraries : libv4l2,
    libraries_private : '-lpthread',
    version : meson.project_version(),
    requires_private : 'libv4lconvert',
    description : 'v4l2 device access library')

v4l2convert_sources = files(
    'v4l2convert.c',
)

v4l2convert_deps = [
    dep_libv4l2,
]

libv4l2privdir = get_option('prefix') / get_option('libdir') / get_option('libv4l2subdir')

v4l2convert = shared_module('v4l2convert',
                            v4l2convert_sources,
                            name_prefix : '',
                            install : true,
                            install_dir : libv4l2privdir,
                            dependencies : v4l2convert_deps,
                            include_directories : v4l2_utils_incdir)

dep_v4l2convert = declare_dependency(link_with : v4l2convert)
