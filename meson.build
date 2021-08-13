project('asmtk', 'cpp',  version : '1.0.0',  default_options : ['warning_level=0'])
add_project_arguments('-Dwarning_level=0', language : 'cpp')
warning_level = 0
inc = include_directories('src/asmtk')


libasmtk_files = []
subdir('src')

# asmjit_dep = dependency('asmjit_vendored')
asmjit = subproject('asmjit_vendored')
asmjit_dep = asmjit.get_variable('asmjit_dep')

libasmtk_depends = [asmjit_dep]

libasmtk = shared_library(
    'asmtk',
    libasmtk_files,
    include_directories : inc,
    dependencies : libasmtk_depends,
    install : true)

asmtk_dep = declare_dependency(include_directories : inc, link_with : libasmtk)