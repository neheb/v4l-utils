utils_common_incdir = include_directories('common')

# Generate targets for media-bus-format-{names,codes}.h
foreach x: [ 'names', 'codes' ]
    output_file = 'media-bus-format-@0@.h'.format(x)
    input_file = 'gen_media_bus_format_@0@.sh'.format(x)
    target = custom_target(
        output_file,
        output : output_file,
        input : input_file,
        command : [
            prog_bash, '@INPUT@', files('..' / 'include' / 'linux' / 'media-bus-format.h'),
        ],
        capture : true,
    )
    set_variable(output_file.underscorify(), target)
endforeach

# Libraries
subdir('libcecutil')
subdir('libmedia_dev')
subdir('libv4l2util')

# Utils
subdir('cec-ctl')
subdir('cec-follower')
subdir('cx18-ctl')
if get_option('libdvbv5')
    subdir('dvb')
endif
subdir('ir-ctl')
subdir('ivtv-ctl')
subdir('keytable')
subdir('media-ctl')
if get_option('qv4l2')
    subdir('qv4l2')
endif
if get_option('qvidcap')
    subdir('qvidcap')
endif
subdir('rds-ctl')
subdir('v4l2-ctl')
subdir('v4l2-dbg')
subdir('v4l2-sysfs-path')

# Compliance tools
subdir('cec-compliance')
if have_fork
    subdir('v4l2-compliance')
endif
