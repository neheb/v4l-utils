libv4l1_sources = files(
    'libv4l1-priv.h',
    'libv4l1.c',
    'log.c',
)

libv4l1_api = files(
    '../include/libv4l1-videodev.h',
    '../include/libv4l1.h',
)

install_headers(libv4l1_api)

libv4l1_deps = [
    dep_libv4l2,
    dep_threads,
]

libv4l1 = shared_library('v4l1',
                         libv4l1_sources,
                         install : true,
                         dependencies : libv4l1_deps,
                         include_directories : v4l2_utils_incdir)

dep_libv4l1 = declare_dependency(link_with : libv4l1)

pkg.generate(
    name : 'libv4l1',
    libraries : libv4l1,
    libraries_private : '-lpthread',
    version : meson.project_version(),
    requires_private : 'libv4l2',
    description : 'v4l1 compatibility library')

v4l1compat_sources = files(
    'v4l1compat.c',
)

v4l1compat_deps = [
    dep_libv4l1,
]

libv4l1privdir = get_option('prefix') / get_option('libdir') / get_option('libv4l1subdir')

v4l1compat = shared_module('v4l1compat',
                           v4l1compat_sources,
                           name_prefix : '',
                           install : true,
                           install_dir : libv4l1privdir,
                           dependencies : v4l1compat_deps,
                           include_directories : v4l2_utils_incdir)

dep_v4l1compat = declare_dependency(link_with : v4l1compat)
