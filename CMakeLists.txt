project(ayatana-indicator-keyboard C CXX)
cmake_minimum_required(VERSION 2.8.12)
list(APPEND CMAKE_MODULE_PATH ${CMAKE_SOURCE_DIR}/cmake)
set(PROJECT_VERSION "0.7.901")
set(PACKAGE ${CMAKE_PROJECT_NAME})

# GNU standard installation directories
include (GNUInstallDirs)
set (CMAKE_INSTALL_PKGLIBEXECDIR "${CMAKE_INSTALL_LIBEXECDIR}/${CMAKE_PROJECT_NAME}")
set (CMAKE_INSTALL_FULL_PKGLIBEXECDIR "${CMAKE_INSTALL_FULL_LIBEXECDIR}/${CMAKE_PROJECT_NAME}")

# Gettext
set(GETTEXT_PACKAGE "ayatana-indicator-keyboard")
add_definitions (-DGETTEXT_PACKAGE="${GETTEXT_PACKAGE}" -DLOCALEDIR="${CMAKE_INSTALL_FULL_LOCALEDIR}")

# Check for prerequisites
find_package (PkgConfig REQUIRED)
include (CheckIncludeFile)
include (FindPkgConfig)
pkg_check_modules(SERVICE_DEPS REQUIRED glib-2.0>=2.36 gio-2.0>=2.36 x11>=1.6.7 libxklavier>=5.4)
include_directories (SYSTEM ${SERVICE_DEPS_INCLUDE_DIRS})

# custom targets
set (ARCHIVE_NAME ${CMAKE_PROJECT_NAME}-${PROJECT_VERSION})
add_custom_target (dist COMMAND bzr export --root=${ARCHIVE_NAME} ${CMAKE_BINARY_DIR}/${ARCHIVE_NAME}.tar.gz WORKING_DIRECTORY ${CMAKE_SOURCE_DIR})
add_custom_target (cppcheck COMMAND cppcheck --enable=all -q --error-exitcode=2 --inline-suppr ${CMAKE_SOURCE_DIR}/src)

# Actual building
if ("${CMAKE_CXX_COMPILER_ID}" STREQUAL "Clang")

  set(C_WARNING_ARGS "${C_WARNING_ARGS} -Weverything")
  set(C_WARNING_ARGS "${C_WARNING_ARGS} -Wno-c++98-compat -Wno-padded")
  set(C_WARNING_ARGS "${C_WARNING_ARGS} -Wno-documentation")

else()

  set(C_WARNING_ARGS "${C_WARNING_ARGS} -Wall -Wformat=2")

endif()

include_directories (${CMAKE_CURRENT_SOURCE_DIR}/include)
include_directories (${CMAKE_CURRENT_BINARY_DIR}/include)

# actually build things
add_subdirectory(src)
add_subdirectory(data)
add_subdirectory(po)
