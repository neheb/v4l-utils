project('v4l-utils', 'c', 'cpp',
    version: '1.21.0',
    meson_version : '>= 0.53',
    default_options : [
        'warning_level=1',
    ],
    license : 'LGPL 2.1+')

cc = meson.get_compiler('c')
cpp = meson.get_compiler('cpp')
have_m32 = cc.has_link_argument('-m32')
have_visibility = cc.has_argument('-fvisibility=hidden')

fs = import('fs')
i18n = import('i18n')
pkg = import('pkgconfig')
qt5 = import('qt5')

as_version = meson.project_version()
ver_arr = as_version.split('.')
as_major_version = ver_arr[0]
as_minor_version = ver_arr[1]
as_patch_version = ver_arr[2]

conf = configuration_data()
conf.set_quoted('V4L_UTILS_VERSION', as_version)
conf.set_quoted('PACKAGE_VERSION', as_version)
conf.set('MAJOR', as_major_version)
conf.set('MINOR', as_minor_version)
conf.set('PATCH', as_patch_version)

if have_visibility
    conf.set('HAVE_VISIBILITY', 1)
endif

common_arguments = [
    '-Wpointer-arith',
    '-D_GNU_SOURCE',
    '-I.', # Needed for config.h
    '-DPROMOTED_MODE_T=int',
]

v4l2_utils_incdir_arr = [
    'lib' / 'include',
]

if host_machine.system() == 'linux'
    v4l2_utils_incdir_arr += 'include'
elif host_machine.system() == 'freebsd'
    v4l2_utils_incdir_arr += 'contrib/freebsd/include'
endif

v4l2_utils_incdir = include_directories(v4l2_utils_incdir_arr)

prog_bash = find_program('bash')
prog_clang = find_program('clang', required : get_option('bpf'))
prog_doxygen = find_program('doxygen', required : get_option('doxygen-doc'))
prog_grep = find_program('grep')
prog_perl = find_program('perl')

dep_alsa = dependency('alsa', required : false)
dep_gl = dependency('gl', required : get_option('qvidcap').enabled() or get_option('qv4l2').enabled())
dep_glu = dependency('glu', required : false)
dep_libdl = cc.find_library('dl')
dep_libelf = cc.find_library('elf', required : get_option('bpf'))
dep_libm = cc.find_library('m')
dep_librt = cc.find_library('rt')
dep_qt5 = dependency('qt5', modules: ['Core', 'Gui', 'Widgets', 'OpenGL'],
                     required : get_option('qvidcap').enabled() or get_option('qv4l2').enabled())
dep_sdl = cc.find_library('SDL2', required : false)
dep_sdl_image = cc.find_library('SDL2_image', required : false)
dep_threads = dependency('threads')
dep_x11 = dependency('x11', required : false)
dep_xmlrpc = dependency('xmlrpc', required : false)

have_fork = cc.has_function('fork')
have_i2c_dev = cc.has_header('linux/i2c-dev.h')

if cc.has_function('argp_parse')
    dep_argp = dependency('', required : false)
else
    dep_argp = cc.find_library('argp')
endif

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

have_gconv = cc.has_header('gconv.h', required : get_option('gconv'))

# Detect system gconv directory
gconvsysdir = get_option('gconvsysdir')

# Generic check: works with most distributions
gconv_dirs_generic = [
    '/lib64',
    '/usr/lib64',
    '/usr/local/lib64',
    '/lib',
    '/usr/lib',
    '/usr/local/lib',
]

if gconvsysdir == ''
    foreach dir : gconv_dirs_generic
        dir = dir / 'gconv'
        if fs.is_dir(dir)
            gconvsysdir = dir
            break
        endif
    endforeach
endif

# Debian/Ubuntu-specific check: should be aligned with the debian package
gconv_dirs_debian = [
    'alphaev67-linux-gnu',
    'arm-linux-gnueabi',
    'arm-linux-gnueabihf',
    'i686-kfreebsd-gnu',
    'i686-linux-gnu',
    'mips-linux-gnu',
    'mips64-linux-gnuabi64',
    'mips64-linux-gnuabin32',
    'mips64el-linux-gnuabi64',
    'mips64el-linux-gnuabin32',
    'mipsel-linux-gnu',
    'mipsisa32r6-linux-gnu',
    'mipsisa32r6el-linux-gnu',
    'mipsisa64r6-linux-gnuabi64',
    'mipsisa64r6-linux-gnuabin32',
    'mipsisa64r6el-linux-gnuabi64',
    'mipsisa64r6el-linux-gnuabin32',
    'powerpc-linux-gnu',
    'powerpc64-linux-gnu',
    's390-linux-gnu',
    'sparc64-linux-gnu',
    'sparcv9-linux-gnu',
    'x86_64-linux-gnu',
    'x86_64-linux-gnux32',
]

if gconvsysdir == ''
    foreach dir : gconv_dirs_debian
        dir = '/usr' / 'lib' / dir / 'gconv'
        if fs.is_dir(dir)
            gconvsysdir = dir
            break
        endif
    endforeach
endif

if gconvsysdir == ''
    dep_jis = cc.find_library('JIS', required : get_option('gconv'))
    dep_jisx0213 = cc.find_library('JISX0213', required : get_option('gconv'))
else
    dep_jis = cc.find_library('JIS', required : get_option('gconv'), dirs : gconvsysdir)
    dep_jisx0213 = cc.find_library('JISX0213', required : get_option('gconv'), dirs : gconvsysdir)
endif

dep_jpeg = dependency('libjpeg', required : get_option('jpeg'))
if dep_jpeg.found()
    dep_jpeg_priv_libs = '-ljpeg'
endif

systemd_systemdir = get_option('systemdsystemunitdir')
if systemd_systemdir == ''
    dep_systemd = dependency('systemd', required : false)
    if dep_systemd.found()
        systemd_systemdir = dep_systemd.get_pkgconfig_variable('systemdsystemunitdir')
    endif
endif
if systemd_systemdir == ''
    systemd_systemdir = '/lib/systemd/system'
endif
# Since systemd v239, udevd is not allowed to execute BPF systems calls;
# add an override to allow bpf(2) in that case. On earlier versions, the
# override will restrict udevd to bpf syscall only and will stop the system
# from booting. This is also true on current debian versions.
have_udevdsyscallfilter = run_command(prog_grep, '-s', 'SystemCallFilter',
                                      systemd_systemdir / 'systemd-udevd.service').returncode() == 0

dep_libudev = dependency('libudev', required : get_option('libdvbv5').enabled())
udevdir = get_option('udevdir')
if udevdir == ''
    dep_udev = dependency('udev', required : false)
    if dep_udev.found()
        udevdir = dep_udev.get_pkgconfig_variable('udevdir')
    endif
endif
if udevdir == ''
    udevdir = '/lib/udev'
endif

qt5_opengl_test = '''
#define GL_GLEXT_PROTOTYPES
#define QT_NO_OPENGL_ES_2

#include <QGLWidget>
#include <QGLShader>
#include <QGLShaderProgram>
#include <QGLFunctions>

#ifndef GL_UNSIGNED_INT_8_8_8_8
#error Missing OpenGL Extensions
#endif
'''
have_qt5_opengl = cpp.compiles(qt5_opengl_test,
                               dependencies : [dep_gl, dep_qt5],
                               args : '-fPIC')

c_arguments = []
cpp_arguments = []

c_arguments += common_arguments
cpp_arguments += common_arguments

add_project_arguments(c_arguments, language : 'c')
add_project_arguments(cpp_arguments, language : 'cpp')
add_project_link_arguments(cpp_arguments, language : 'cpp')

version_h = vcs_tag(command : ['gen-version.sh', meson.build_root()],
                    input : 'version.h.in',
                    output : 'version.h')

man_pages = []

i18n_gettext_arguments = ['--directory=' + meson.source_root(), '--keyword=_', '--keyword=N_', '--keyword=P_:1,2']
subdir('libdvbv5-po')
subdir('v4l-utils-po')

subdir('lib')

if get_option('v4l-utils')
    subdir('utils')
    subdir('contrib')
endif

subdir('doc')

configure_file(output : 'config.h', configuration : conf)

foreach m : man_pages
    configure_file(input : join_paths(m[0], '@0@.@1@.in'.format(m[1], m[2])),
                   output : '@0@.@1@'.format(m[1], m[2]),
                   install_dir : join_paths(get_option('mandir'), 'man@0@'.format(m[2])),
                   configuration : conf)
endforeach