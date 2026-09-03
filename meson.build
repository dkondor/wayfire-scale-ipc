project(
	'wayfire_scale_ipc',
	'cpp',
	version: '1.1.0',
	license: 'Unlicense',
	meson_version: '>=0.51.0',
	default_options: [
		'cpp_std=c++17',
		'warning_level=2',
		'werror=false',
	],
)

# dependencies for loadable plugin
wayfire  = dependency('wayfire', version: '>=0.10.0')

if get_option('local_build')
	lib_dir = get_option('prefix') / 'lib' / 'wayfire'
	metadata_dir = get_option('prefix') / 'share' / 'wayfire' / 'metadata'
else
	lib_dir = wayfire.get_variable(pkgconfig: 'plugindir')
	metadata_dir = wayfire.get_variable(pkgconfig: 'metadatadir')
endif

sources = ['scale_ipc_filter.cpp']
scale_ipc_filter_lib = shared_module('scale_ipc_filter', sources,
    dependencies: [wayfire],
    install: true,
    install_dir: lib_dir,
    cpp_args: ['-Wno-unused-parameter', '-Wno-format-security','-DWAYFIRE_PLUGIN'],
    link_args: '-rdynamic')

install_data('scale_ipc_filter.xml', install_dir: metadata_dir)
