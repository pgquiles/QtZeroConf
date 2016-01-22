project(QtZeroConf)

cmake_minimum_required(VERSION 3.1)

list(APPEND CMAKE_MODULE_PATH ${QtZeroConf_SOURCE_DIR}/cmake)

set(CMAKE_INCLUDE_CURRENT_DIR ON)
set(CMAKE_AUTOMOC ON)

find_package(Qt5Core REQUIRED)
find_package(Qt5Network REQUIRED)

set(QTZEROCONF_HDRS)
set(QTZEROCONF_SRCS)
set(QTZEROCONF_LIBS)

if(CMAKE_SYSTEM_NAME MATCHES Linux)
   find_package(Avahi REQUIRED)
   list(APPEND QTZEROCONF_HDRS qzeroconf.h avahi-qt/qt-watch.h avahi-qt/qt-watch_p.h)
   list(APPEND QTZEROCONF_SRCS avahiclient.cpp avahi-qt/qt-watch.cpp)
   list(APPEND QTZEROCONF_LIBS ${AVAHI_LIBRARIES})
   include_directories(${QtZeroConf_SOURCE_DIR})
endif()


if(WIN32)
   add_definitions(-DWIN32 -DNDEBUG -D_WINDOWS -D_USRDLL -DMDNS_DEBUGMSGS=0 -DWIN32_LEAN_AND_MEAN -DUSE_TCP_LOOPBACK -D_CRT_SECURE_NO_DEPRECATE -D_CRT_SECURE_CPP_OVERLOAD_STANDARD_NAMES=1 -DNOT_HAVE_SA_LEN)
   include_directories(${QtZeroConf_SOURCE_DIR} ${QtZeroConf_SOURCE_DIR}/bonjour-sdk)
   list(APPEND QTZEROCONF_HDRS qzeroconf.h $bonjour_p.h)
   list(APPEND QTZEROCONF_SRCS bonjour.cpp bonjour-sdk/dnssd_clientlib.c bonjour-sdk/dnssd_clientstub.c bonjour-sdk/dnssd_ipc.c)
   find_library(WS2_32_LIBRARY ws2_32)
   find_library(WSOCK32_LIBRARY wsock32)
   list(APPEND QTZEROCONF_LIBS ${WS2_32_LIBRARY} ${WSOCK32_LIBRARY})
endif()

if(APPLE)
   list(APPEND ZEROCONF_HDRS qzeroconf.h bonjour_p.h)
   list(APPEND ZEROCONF_SRCS bonjour.cpp)
   find_library(CORESERVICES_LIBRARY CoreServices)
   list(APPEND QTZEROCONF_LIBS ${CORESERVICES_LIBRARY})
   include_directories(${QtZeroConf_SOURCE_DIR})
endif()

if(CMAKE_SYSTEM_NAME MATCHES Android)
   include_directories(${QtZeroConf_SOURCE_DIR})

   list(APPEND QTZEROCONF_HDRS qzeroconf.h avahi-qt/qt-watch.h avahi-qt/qt-watch_p.h)
   
   list(APPEND QTZEROCONF_SRCS 
        avahicore.cpp
        avahi-qt/qt-watch.cpp
	avahi-common/address.c
	avahi-common/alternative.c
	avahi-common/domain.c
	avahi-common/error.c
	avahi-common/i18n.c
	avahi-common/malloc.c
	avahi-common/rlist.c
	avahi-common/simple-watch.c
	avahi-common/strlst.c
	avahi-common/thread-watch.c
	avahi-common/timeval.c
	avahi-common/utf8.c
	avahi-core/addr-util.c
	avahi-core/announce.c
	avahi-core/browse.c
	avahi-core/browse-dns-server.c
	avahi-core/browse-domain.c
	avahi-core/browse-service.c
	avahi-core/browse-service-type.c
	avahi-core/cache.c
	avahi-core/dns.c
	avahi-core/domain-util.c
	avahi-core/entry.c
	avahi-core/fdutil.c
	avahi-core/hashmap.c
	avahi-core/iface.c
	avahi-core/iface-linux.c
	avahi-core/log.c
	avahi-core/multicast-lookup.c
	avahi-core/netlink.c
	avahi-core/prioq.c
	avahi-core/probe-sched.c
	avahi-core/querier.c
	avahi-core/query-sched.c
	avahi-core/resolve-address.c
	avahi-core/resolve-host-name.c
	avahi-core/resolve-service.c
	avahi-core/response-sched.c
	avahi-core/rr.c
	avahi-core/rrlist.c
	avahi-core/server.c
	avahi-core/socket.c
	avahi-core/timeeventq.c
	avahi-core/util.c
	avahi-core/wide-area.c
   )

   add_definitions(-DHAVE_STRLCPY -DGETTEXT_PACKAGE -DHAVE_NETLINK)
endif()

add_library(QtZeroConf SHARED ${QTZEROCONF_SRCS} ${QTZEROCONF_HDRS})
target_link_libraries(QtZeroConf Qt5::Core Qt5::Network ${QTZEROCONF_LIBS})
set_target_properties(QtZeroConf PROPERTIES VERSION 1.0 SOVERSION 1)

add_subdirectory(example)

install(TARGETS QtZeroConf LIBRARY DESTINATION lib)
