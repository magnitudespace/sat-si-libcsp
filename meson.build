project('csp', 'c', version: '1.5.0', license: 'LGPL')

cc = meson.get_compiler('c')

conf = configuration_data()

conf.set('CSP_DEBUG', 1)
conf.set('CSP_LOG_LEVEL_DEBUG', 1)
conf.set('CSP_LOG_LEVEL_INFO', 1)
conf.set('CSP_LOG_LEVEL_WARN', 1)
conf.set('CSP_LOG_LEVEL_ERROR', 1)

conf.set('CSP_VERSION', get_option('version'))
conf.set('CSP_PACKET_PADDING_BYTES', get_option('packet_padding_bytes'))

conf.set('CSP_USE_RDP', get_option('use_rdp') == true ? 1 : 0)
conf.set('CSP_USE_CRC32', get_option('use_crc32') == true ? 1 : 0)
conf.set('CSP_USE_HMAC', get_option('use_hmac') == true ? 1 : 0)
conf.set('CSP_USE_XTEA', get_option('use_xtea') == true ? 1 : 0)
conf.set('CSP_USE_PROMISC', get_option('use_promisc') == true ? 1 : 0)
conf.set('CSP_USE_DEDUP', get_option('use_dedup') == true ? 1 : 0)

conf.set('CSP_HAVE_STDBOOL_H', cc.has_header('stdbool.h') == true ? 1 : 0)

zmq_dep = dependency('libzmq', version: '>4.0', required: false)
if zmq_dep.found()
	conf.set('CSP_HAVE_LIBZMQ', 1)
endif

if host_machine.endian() == 'little'
	conf.set('CSP_LITTLE_ENDIAN', 1)
else
	conf.set('CSP_BIG_ENDIAN', 1)
endif

csp_sources = files([
	'src/crypto/csp_hmac.c',
	'src/crypto/csp_sha1.c',
	'src/crypto/csp_xtea.c',
	'src/transport/csp_rdp.c',
	'src/transport/csp_udp.c',
	'src/csp_buffer.c',
	'src/csp_bridge.c',
	'src/csp_conn.c',
	'src/csp_crc32.c',
	'src/csp_debug.c',
	'src/csp_dedup.c',
	'src/csp_endian.c',
	'src/csp_hex_dump.c',
	'src/csp_iflist.c',
	'src/csp_init.c',
	'src/csp_io.c',
	'src/csp_port.c',
	'src/csp_promisc.c',
	'src/csp_qfifo.c',
	'src/csp_route.c',
	'src/csp_service_handler.c',
	'src/csp_services.c',
	'src/csp_id.c',
])

csp_deps = []
subdir('src/rtable')
subdir('src/arch')
subdir('src/drivers')
subdir('src/interfaces')

csp_config_h = configure_file(output: 'csp_autoconfig.h', configuration: conf, install: true, install_dir: 'csp/')

csp_inc = include_directories(['.', 'include'])
install_subdir('include', install_dir : '.')
install_headers(csp_config_h, install_dir : 'include/csp')

csp_deps += dependency('freertos', required: false)

csp_lib = static_library('csp',
	sources: [csp_sources, csp_config_h],
	include_directories : csp_inc,
	dependencies : csp_deps,
)

csp_dep = declare_dependency(
	include_directories : csp_inc,
	link_with : csp_lib,
)

csp_dep_whole = declare_dependency(
	include_directories : csp_inc,
	link_whole : csp_lib,
)
