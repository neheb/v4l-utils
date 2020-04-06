bpf_protocols_files = [
    'grundig',
    'imon_rsc',
    'manchester',
    'pulse_distance',
    'pulse_length',
    'raw',
    'rc_mm',
    'samsung36',
    'xbox-dvd',
]

clang_sys_includes = run_command('clang_sys_includes.sh',
                                 check : true,
                                 env : ['CLANG=' + prog_clang.path()])

foreach file : bpf_protocols_files
    output = file + '.o'
    input = file + '.c'
    custom_target(output,
                  output : output,
                  input : input,
                  command : [
                      prog_clang.path(),
                      clang_sys_includes.stdout().split(),
                      '-D__linux__', '-target', 'bpf', '-O2',
                      '-c', '@INPUT@', '-o', '@OUTPUT@',
                  ],
                  install : true,
                  install_dir : ir_keytable_system_dir / 'rc_keymaps' / 'protocols')
endforeach

# As per meson version 0.50.0, there's no direct method to install an empty
# directory. A simple work-around, let's install the following dummy directory
# with no files in it.
install_subdir('user_dir_protocols',
               exclude_files : 'README.md',
               strip_directory : true,
               install_dir : ir_keytable_user_dir / 'protocols')
