project('tsdb', 'cpp',
	version: '2.4.1',
	default_options: ['cpp_std=c++17'],
	meson_version: '>=0.52.0')


summary_str = '''# date build summary:
  - using system tz:   @0@
  - save tz in cwd:    @1@
  - test:              @2@
  - using string view: @3@
'''.format(
	get_option('system_tz'),
	get_option('tz_db_in_dot'),
	get_option('test'),
	get_option('string_view'),
)

message(summary_str)


date_args = [
	'-DONLY_C_LOCALE=' + (get_option('use_c_locale') ? '1' : '0'),
]

if not get_option('string_view')
	date_args += '-DHAS_STRING_VIEW=0'
endif

# date (header only) library
date_dep = declare_dependency(
	include_directories: include_directories('include'),
	compile_args: date_args)


use_dll = host_machine.system() == 'windows' and get_option('library_type') == 'shared'
use_system_tz = get_option('system_tz') and not host_machine.system() == 'windows'

tz_private_args = [
	'-DUSE_AUTOLOAD=' + (get_option('system_tz') ? '0' : '1'),
	'-DHAS_REMOTE_API=' + (get_option('system_tz') ? '0' : '1'),
	'-DDATE_BUILD_DLL=' + (use_dll ? '1' : '0'),
	'-DUSE_OS_TZDB=' + (use_system_tz ? '1' : '0'),
]
tz_public_args = [
	'-DUSE_OS_TZDB=' + (use_system_tz ? '1' : '0'),
]

if get_option('tz_db_in_dot')
	tz_private_args += 'INSTALL=.'
endif

if use_dll
	tz_public_args += '-DDATE_USE_DLL=1'
endif

tz_internal_deps = [dependency('threads')]
if not get_option('system_tz')
	tz_internal_deps += dependency('libcurl')
endif

tz_lib = library('tz',
	dependencies: tz_internal_deps,
	include_directories: include_directories('include'),
	cpp_args: tz_private_args)
tz_dep = declare_dependency(
	link_with: tz_lib,
	dependencies: tz_internal_deps,
	include_directories: include_directories('include'),
	compile_args: tz_private_args)

# WIP: install and tests
