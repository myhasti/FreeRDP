# FreeRDP: A Remote Desktop Protocol Client
# libfreerdp-core cmake build script
#
# Copyright 2011 O.S. Systems Software Ltda.
# Copyright 2011 Otavio Salvador <otavio@ossystems.com.br>
# Copyright 2012 Marc-Andre Moreau <marcandre.moreau@gmail.com>
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

add_definitions(-DEXT_PATH="${FREERDP_EXTENSION_PATH}")

include_directories(${OPENSSL_INCLUDE_DIR})
include_directories(${ZLIB_INCLUDE_DIRS})

set(FREERDP_CORE_SRCS
	activation.c
	activation.h
	extension.c
	extension.h
	gcc.c
	gcc.h
	mcs.c
	mcs.h
	nego.c
	nego.h
	info.c
	info.h
	http.c
	http.h
	rpc.c
	rpc.h
	rts.c
	rts.h
	input.c
	input.h
	license.c
	license.h
	errinfo.c
	errinfo.h
	security.c
	security.h
	settings.c
	orders.c
	orders.h
	freerdp.c
	graphics.c
	capabilities.c
	capabilities.h
	certificate.c
	certificate.h
	connection.c
	connection.h
	redirection.c
	redirection.h
	timezone.c
	timezone.h
	rdp.c
	rdp.h
	tcp.c
	tcp.h
	tsg.c
	tsg.h
	tpdu.c
	tpdu.h
	tpkt.c
	tpkt.h
	fastpath.c
	fastpath.h
	surface.c
	surface.h
	transport.c
	transport.h
	update.c
	update.h
	channel.c
	channel.h
	window.c
	window.h
	listener.c
	listener.h
	peer.c
	peer.h)

if(WITH_MONOLITHIC_BUILD)
	add_library(freerdp-core OBJECT ${FREERDP_CORE_SRCS})
else()
	add_library(freerdp-core ${FREERDP_CORE_SRCS})
endif()

set_target_properties(freerdp-core PROPERTIES VERSION ${FREERDP_VERSION_FULL} SOVERSION ${FREERDP_VERSION} PREFIX "lib")

if(WITH_MONOLITHIC_BUILD)
	set(FREERDP_CORE_LIBS winpr)
else()
	set(FREERDP_CORE_LIBS
		winpr-utils
		winpr-rpc
		winpr-sspi)
	
	if(NOT WIN32)
		set(FREERDP_CORE_LIBS ${FREERDP_CORE_LIBS} winpr-registry)
	endif()
endif()

if(WIN32)
	set(FREERDP_CORE_LIBS ${FREERDP_CORE_LIBS} ws2_32)
else()
	set(FREERDP_CORE_LIBS ${FREERDP_CORE_LIBS} ${ZLIB_LIBRARIES})
endif()

set(FREERDP_CORE_LIBS ${FREERDP_CORE_LIBS} ${OPENSSL_LIBRARIES})

if(WITH_MONOLITHIC_BUILD)
	set(FREERDP_LIBS ${FREERDP_LIBS} ${FREERDP_CORE_LIBS} PARENT_SCOPE)
else()
	set(FREERDP_CORE_LIBS ${FREERDP_CORE_LIBS}
		freerdp-utils
		freerdp-codec
		freerdp-crypto
		freerdp-locale)
		
	target_link_libraries(freerdp-core ${FREERDP_CORE_LIBS})
	install(TARGETS freerdp-core DESTINATION ${CMAKE_INSTALL_LIBDIR})
endif()
