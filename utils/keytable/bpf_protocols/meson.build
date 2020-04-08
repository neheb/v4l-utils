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
                      clang_sys_includes.stdout().strip(),
                      '-D__linux__', '-target', 'bpf', '-O2',
                      '-c', '@INPUT@', '-o', '@OUTPUT@',
                  ],
                  install : true,
                  install_dir : udevdir / 'rc_keymaps' / 'protocols')
endforeach
