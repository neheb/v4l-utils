project('v4l-utils', 'c', 'cpp',
    version: '1.19.0',
    default_options : [
        'c_std=c99',
        'cpp_std=c++98',
        'warning_level=2',
    ],
    license : 'LGPL 2.1+')

cc = meson.get_compiler('c')
have_m32 = cc.has_link_argument('-m32')

pkg = import('pkgconfig')
qt5 = import('qt5')

as_version = meson.project_version()
ver_arr = as_version.split('.')
as_major_version = ver_arr[0]
as_minor_version = ver_arr[1]
as_patch_version = ver_arr[2]

conf = configuration_data()
conf.set_quoted('V4L_UTILS_VERSION', as_version)
conf.set('PACKAGE_VERSION', as_version)
conf.set('MAJOR', as_major_version)
conf.set('MINOR', as_minor_version)
conf.set('PATCH', as_patch_version)

common_arguments = [
    '-Wpointer-arith',
    '-D_GNU_SOURCE',
    '-I.', # Needed for config.h
    '-DPROMOTED_MODE_T=int'
]

v4l2_utils_incdir = include_directories(
    'include',
    'lib' / 'include'
)

prog_bash = find_program('bash')
prog_clang = find_program('clang')
prog_grep = find_program('grep')
prog_perl = find_program('perl')

dep_alsa = dependency('alsa', required : false)
dep_libdl = cc.find_library('dl')
dep_libelf = cc.find_library('elf', required : false)
dep_libm = cc.find_library('m')
dep_librt = cc.find_library('rt')
dep_opengl = dependency('OpenGL', required : false)
dep_qt5 = dependency('qt5', modules: ['Core', 'Gui', 'Widgets', 'OpenGL'], required : false)
dep_threads = dependency('threads')
dep_xmlrpc = dependency('xmlrpc', required : false)

have_fork = cc.has_function('fork')

if cc.has_function('iconv_open')
    dep_iconv = dependency('', required : false)
    have_iconv = true
else
    dep_iconv = cc.find_library('iconv', required : false)
    have_iconv = dep_iconv.found()
endif

if have_iconv
    conf.set('HAVE_ICONV', 1)
    iconv_const_test = '''#include <iconv.h>
size_t iconv (iconv_t cd, char * *inbuf, size_t *inbytesleft, char * *outbuf, size_t *outbytesleft);
'''
    if cc.compiles(iconv_const_test, dependencies : dep_iconv)
        conf.set('ICONV_CONST', '')
    else
        conf.set('ICONV_CONST', 'const')
    endif
endif

dep_jpeg = dependency('libjpeg', required : get_option('jpeg'))
if dep_jpeg.found()
    dep_jpeg_priv_libs = '-ljpeg'
endif

systemd_systemdir = get_option('systemdsystemunitdir')
if systemd_systemdir == ''
    systemd_systemdir = dependency('systemd', required : false).get_pkgconfig_variable('systemdsystemunitdir')
endif
if systemd_systemdir == ''
    systemd_systemdir = '/lib/systemd/system'
endif
# Since systemd v239, udevd is not allowed to execute BPF systems calls;
# add an override to allow bpf(2) in that case. On earlier versions, the
# override will restrict udevd to bpf syscall only and will stop the system
# from booting. This is also true on current debian versions.
have_udevdsyscallfilter = run_command(prog_grep, '-s', 'SystemCallFilter', systemd_systemdir / 'systemd-udevd.service')

dep_udev = dependency('libudev', required : get_option('udev'))
udevdir = get_option('udevdir')
if udevdir == ''
    udevdir = dependency('udev', required : false).get_pkgconfig_variable('udevdir')
endif
if udevdir == ''
    udevdir = '/lib/udev'
endif

c_arguments = []
cpp_arguments = [
'-std=c++98',
]

c_arguments += common_arguments
cpp_arguments += common_arguments

add_project_arguments(c_arguments, language : 'c')
add_project_arguments(cpp_arguments, language : 'cpp')
add_project_link_arguments(cpp_arguments, language : 'cpp')

version_h = vcs_tag(command : ['gen-version.sh', meson.build_root()],
                    input : 'version.h.in',
                    output : 'version.h')

man_pages = []

subdir('lib')

if get_option('v4l-utils')
    subdir('utils')
endif

configure_file(output : 'config.h', configuration : conf)

foreach m : man_pages
    configure_file(input : join_paths(m[0], '@0@.@1@.in'.format(m[1], m[2])),
                   output : '@0@.@1@'.format(m[1], m[2]),
                   install_dir : join_paths(get_option('mandir'), 'man@0@'.format(m[2])),
                   configuration : conf)
endforeach
