cmake_minimum_required(VERSION 3.30.5)

project(ft-ping
    VERSION 1.0.15
    LANGUAGES C
)

set(CMAKE_C_STANDARD 11)
set(CMAKE_C_STANDARD_REQUIRED ON)
set(CMAKE_C_EXTENSIONS ON)

if(CMAKE_BUILD_TYPE STREQUAL "Release")
    add_link_options(-s)
endif()

set(CMAKE_RUNTIME_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/bin")
set(SRC "${CMAKE_CURRENT_SOURCE_DIR}/src")

set(HEADER_FILES ${SRC}/ft_ping.h)
set(SRC_FILES ${SRC}/utils.c ${SRC}/verbose.c ${SRC}/parse.c ${SRC}/recv.c ${SRC}/ft_ping.c)


add_executable(ft-ping 
    ${HEADER_FILES}
    ${SRC_FILES}
)

target_include_directories(ft-ping PRIVATE
    "${SRC}"
    "/usr/include"
)

target_compile_options(ft-ping PRIVATE
    "SHELL:-include sys/time.h"
    "SHELL:-include netdb.h"
    "SHELL:-include netinet/ip.h"
    "SHELL:-include netinet/icmp6.h"
)

if(UNIX AND NOT APPLE)
    target_link_libraries(ft-ping PRIVATE m)
endif()

include(GNUInstallDirs)

add_custom_command(
    OUTPUT "${CMAKE_CURRENT_BINARY_DIR}/changelog.gz"
    COMMAND gzip -cn9 "${CMAKE_CURRENT_SOURCE_DIR}/changelog" > "${CMAKE_CURRENT_BINARY_DIR}/changelog.gz"
    WORKING_DIRECTORY "${CMAKE_CURRENT_SOURCE_DIR}"
    DEPENDS "${CMAKE_CURRENT_SOURCE_DIR}/changelog"
    COMMENT "Compressing changelog"
)

add_custom_target(changelog ALL DEPENDS "${CMAKE_CURRENT_BINARY_DIR}/changelog.gz")

install(FILES "${CMAKE_CURRENT_BINARY_DIR}/changelog.gz"
    DESTINATION "${CMAKE_INSTALL_DOCDIR}"
)

install(TARGETS ft-ping
    RUNTIME DESTINATION "${CMAKE_INSTALL_BINDIR}"
)

#install(FILES man/ft-ping.1 DESTINATION "${CMAKE_INSTALL_MANDIR}/man1")
install(FILES man/ft-ping.1 TYPE MAN)

install(FILES
    files/checksum.png
    files/make.png
    files/makedebug.png
    DESTINATION "${CMAKE_INSTALL_DATADIR}/ft-ping"
)

install(FILES LICENSE DESTINATION "${CMAKE_INSTALL_DOCDIR}" RENAME copyright)

# if(EXISTS "${CMAKE_CURRENT_SOURCE_DIR}/LICENSE")
#     install(FILES LICENSE
#         DESTINATION "${CMAKE_INSTALL_DATADIR}/doc/ft-ping"
#     )
# endif()

set(CPACK_RPM_EXCLUDE_FROM_AUTO_FILELIST_ADDITION
    "/usr/share/man"
    "/usr/share/man/man1"
)

set(CPACK_PACKAGE_NAME "ft-ping")
set(CPACK_PACKAGE_VENDOR "ft-ping project")
set(CPACK_PACKAGE_DESCRIPTION_SUMMARY "Simple ping implementation")
set(CPACK_RPM_PACKAGE_DESCRIPTION "ft-ping is a simple implementation of the ping utility.\nIt sends ICMP echo requests and measures response time.")
set(CPACK_PACKAGE_CONTACT "KateZz <katezaytsewa@gmail.com>")
set(CPACK_RPM_PACKAGE_PACKAGER "Kate Zz <katezaytsewa@gmail.com>")
set(CPACK_PACKAGE_VERSION "${PROJECT_VERSION}")
set(CPACK_RESOURCE_FILE_LICENSE "${CMAKE_CURRENT_SOURCE_DIR}/LICENSE")
set(CPACK_GENERATOR "DEB;RPM")

set(CPACK_DEBIAN_PACKAGE_DESCRIPTION "ft-ping is a simple implementation of the ping utility.
It sends ICMP echo requests and measures response time.
This package provides the command-line tool for network diagnostics.")
set(CPACK_DEBIAN_PACKAGE_MAINTAINER "KateZz <katezaytsewa@gmail.com>")
set(CPACK_DEBIAN_PACKAGE_SECTION "net")
set(CPACK_DEBIAN_PACKAGE_HOMEPAGE "https://example.com/ft_ping")
set(CPACK_DEBIAN_FILE_NAME DEB-DEFAULT)
set(CPACK_DEBIAN_PACKAGE_SHLIBDEPS ON)
set(CPACK_DEBIAN_PACKAGE_DEPENDS "libc6 (>= 2.34)")

set(CPACK_RPM_PACKAGE_LICENSE "Apache")
set(CPACK_RPM_PACKAGE_GROUP "Applications/Internet")
set(CPACK_RPM_PACKAGE_URL "https://example.com/ft_ping")
set(CPACK_RPM_FILE_NAME "RPM-DEFAULT")

include(CPack)
