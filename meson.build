project(
	'wayfire_scale_ipc',
	'cpp',
	version: '0.0.1',
	license: 'Unlicense',
	meson_version: '>=0.51.0',
	default_options: [
		'cpp_std=c++17',
		'warning_level=2',
		'werror=false',
	],
)

# dependencies for loadable plugin
wayfire  = dependency('wayfire')
# wlroots  = dependency('wlroots')
# wlserver = dependency('wayland-server')
json     = dependency('nlohmann_json', required: true)

sources = ['scale_ipc_filter.cpp']
scale_ipc_filter_lib = shared_module('scale_ipc_filter', sources,
    dependencies: [wayfire, json],
    install: true,
    install_dir: wayfire.get_variable(pkgconfig: 'plugindir'),
    cpp_args: ['-Wno-unused-parameter', '-Wno-format-security','-DWAYFIRE_PLUGIN'],
    link_args: '-rdynamic')

install_data('scale_ipc_filter.xml', install_dir: wayfire.get_variable(pkgconfig: 'metadatadir'))
