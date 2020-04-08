libv4l2rds_sources = files(
    'libv4l2rds.c',
)

libv4l2rds_api = files(
    '../include/libv4l2rds.h',
)

install_headers(libv4l2rds_api)

libv4l2rds_deps = [
    dep_threads,
]

libv4l2rds = shared_library('v4l2rds',
                            libv4l2rds_sources,
                            install : true,
                            dependencies : libv4l2rds_deps,
                            include_directories : v4l2_utils_incdir)

dep_libv4l2rds = declare_dependency(link_with : libv4l2rds)

pkg.generate(
    name : 'libv4l2rds',
    libraries : libv4l2rds,
    libraries_private : '-lpthread',
    version : meson.project_version(),
    description : 'v4l2 RDS decode library')
