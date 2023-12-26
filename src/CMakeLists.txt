cmake_minimum_required(VERSION 3.5)  # lowest version tried
#新增加===
#DEBUG增加后缀
set(CMAKE_DEBUG_POSTFIX "d")
set(LIBHEIF_PATH "C:\\code\\vcpkg\\installed\\x64-windows")
#set(QT_PATH "E:\\Qt\\5.12.6\\msvc2017_64" CACHE PATH "qt6 cmake dir")
#set(CMAKE_PREFIX_PATH ${QT_PATH})
 
###此处路径添加你自己的
set(LIBHEIF_INC_DIR ${LIBHEIF_PATH}\\include)
set(LIBHEIF_LIB_DIR ${LIBHEIF_PATH}\\lib)
#设置附加头文件
include_directories(${LIBHEIF_INC_DIR})
#设置附加库目录
link_directories(${LIBHEIF_LIB_DIR})
#新增加===
 
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)
 
# make release build, if not specified
# (from https://blog.kitware.com/cmake-and-the-default-build-type/)
set(default_build_type "RelWithDebInfo")
 
if (NOT CMAKE_BUILD_TYPE AND NOT CMAKE_CONFIGURATION_TYPES)
  set(CMAKE_BUILD_TYPE "${default_build_type}" CACHE STRING "Build type" FORCE)
  set_property(
    CACHE CMAKE_BUILD_TYPE PROPERTY STRINGS
    "Debug" "Release" "MinSizeRel" "RelWithDebInfo")
endif ()
 
# compiler flags
# TODO: separate GCC and Clang warnings; add more
#修改==
set(
  CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} \
  -Wall \
  ")
#set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} -Og")
#修改===
 
set(sanitizer_flags "-fsanitize=address -fsanitize=undefined")
set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} ${sanitizer_flags}")
set(CMAKE_MODULE_LINKER_FLAGS_DEBUG
    "${CMAKE_MODULE_LINKER_FLAGS_DEBUG} ${sanitizer_flags}")
 
#
# third-party libs
#
 
# qt
find_package(Qt6 COMPONENTS Core Gui REQUIRED)
add_definitions(-DQT_NO_KEYWORDS)
set(CMAKE_AUTOMOC ON)
 
# libheif
#修改==
#find_package(PkgConfig)
#pkg_check_modules(libheif REQUIRED libheif>=1.1)
 
set (LIB_HEIF heif libx265 de265.lib) 
#修改==
#
# project source
#
 
set(CMAKE_INCLUDE_CURRENT_DIR ON)
 
set(sources main.cpp qheifhandler.cpp)
 
 
add_library(qheif MODULE ${sources})
#修改==
target_link_libraries(
  qheif
  PRIVATE
  Qt6::Gui
  ${LIB_HEIF}
)
#修改==
 
#
# installation
#
 
# Use qmake to find plugin dir (adapted from lxqt-qtplugin)
get_target_property(
  QT_QMAKE_EXECUTABLE Qt6::qmake IMPORTED_LOCATION)
 
if (NOT QT_QMAKE_EXECUTABLE)
  message(FATAL_ERROR "qmake is not found.")
endif ()
 
execute_process(
  COMMAND ${QT_QMAKE_EXECUTABLE} -query QT_INSTALL_PLUGINS
  OUTPUT_VARIABLE QT_PLUGINS_DIR
  OUTPUT_STRIP_TRAILING_WHITESPACE)
 
if (QT_PLUGINS_DIR)
  message(STATUS "Qt6 plugin directory: " "${QT_PLUGINS_DIR}")
else ()
  message(FATAL_ERROR "Qt6 plugin directory cannot be detected.")
endif ()
 
# Prefix with DESTDIR if available to allow packaging
if (ENV{DESTDIR} AND NOT ENV{DESTDIR} STREQUAL "")
  set(plugins_dir "$ENV{DESTDIR}${QT_PLUGINS_DIR}")
else ()
  set(plugins_dir "${QT_PLUGINS_DIR}")
endif ()
 
install(
  TARGETS qheif
  LIBRARY DESTINATION "${plugins_dir}/imageformats")
 
# vim:sw=2 