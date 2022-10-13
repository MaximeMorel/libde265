cmake_minimum_required (VERSION 3.3.2)

project (libde265
    LANGUAGES C CXX
    VERSION 1.0.8
)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)
set(CMAKE_POSITION_INDEPENDENT_CODE ON)

# The version number.
set (NUMERIC_VERSION 0x01000800)
set (PACKAGE_VERSION ${PROJECT_VERSION})

include (${CMAKE_ROOT}/Modules/CheckCCompilerFlag.cmake)
include (${CMAKE_ROOT}/Modules/CheckIncludeFile.cmake)

include(GNUInstallDirs)
include(CheckFunctionExists)

option(ENABLE_SDL "Enable SDL" ON)

if (ENABLE_SDL)
	find_package(SDL)
endif()

find_package(Threads REQUIRED)

CHECK_INCLUDE_FILE(malloc.h HAVE_MALLOC_H)
CHECK_INCLUDE_FILE(stdint.h HAVE_STDINT_H)
CHECK_INCLUDE_FILE(stdbool.h HAVE_STDBOOL_H)
CHECK_FUNCTION_EXISTS(posix_memalign HAVE_POSIX_MEMALIGN)

if (HAVE_MALLOC_H)
  add_definitions(-DHAVE_MALLOC_H)
endif()
if (HAVE_STDINT_H)
  add_definitions(-DHAVE_STDINT_H)
endif()
if (HAVE_STDBOOL_H)
  add_definitions(-DHAVE_STDBOOL_H)
endif()
if (HAVE_POSIX_MEMALIGN)
  add_definitions(-DHAVE_POSIX_MEMALIGN)
endif()

configure_file (libde265/de265-version.h.in libde265/de265-version.h)

if(CMAKE_COMPILER_IS_GNUCXX OR ${CMAKE_CXX_COMPILER_ID} MATCHES Clang)
  add_definitions(-Wall)
endif()

option(DISABLE_SSE "Disable SSE optimizations" OFF)

option(BUILD_SHARED_LIBS "Build shared library" ON)
if(NOT BUILD_SHARED_LIBS)
  add_definitions(-DLIBDE265_STATIC_BUILD)
endif()

include_directories ("${PROJECT_SOURCE_DIR}")
include_directories ("${PROJECT_BINARY_DIR}")
include_directories ("${PROJECT_SOURCE_DIR}/libde265")
if(MSVC)
  include_directories ("${PROJECT_SOURCE_DIR}/extra")
  add_definitions(-DHAVE_STDINT_H)
  add_definitions(-DHAVE_STDBOOL_H)
endif()

add_subdirectory (libde265)

option(HAVE_ARM "Enable arm architecture" OFF)
if (HAVE_ARM)
  add_definitions(-DHAVE_ARM)
endif()

option(WITH_DEC265 "Build dec265 decoder" ON)
if (WITH_DEC265)
  add_subdirectory (dec265)
endif()
option(WITH_ENC265 "Build enc265 encoder" ON)
if (WITH_ENC265)
  add_subdirectory (enc265)
endif()

set(prefix ${CMAKE_INSTALL_PREFIX})
set(exec_prefix ${CMAKE_INSTALL_PREFIX})
set(libdir ${CMAKE_INSTALL_FULL_LIBDIR})
set(includedir ${CMAKE_INSTALL_FULL_INCLUDEDIR})
set(VERSION ${PROJECT_VERSION})
configure_file(libde265.pc.in ${CMAKE_CURRENT_BINARY_DIR}/libde265.pc @ONLY)
install(FILES ${CMAKE_CURRENT_BINARY_DIR}/libde265.pc
        DESTINATION ${CMAKE_INSTALL_LIBDIR}/pkgconfig)
