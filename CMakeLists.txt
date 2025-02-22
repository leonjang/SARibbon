﻿
# Cmake的命令不区分打下写，例如message，set等命令；但Cmake的变量区分大小写
# 为统一风格，本项目的Cmake命令全部采用小写，变量全部采用大写加下划线组合。

cmake_minimum_required(VERSION 3.5)

set(SARIBBON_VERSION_MAJOR 0)
set(SARIBBON_VERSION_MINOR 6)
set(SARIBBON_VERSION_PATCH 1)
set(SARIBBON_VERSION "${SARIBBON_VERSION_MAJOR}.${SARIBBON_VERSION_MINOR}.${SARIBBON_VERSION_PATCH}")
project(SARibbon VERSION ${SARIBBON_VERSION} LANGUAGES CXX)

# option(BUILD_SHARED_LIBS "build the SARibbonBar in shared lib mode" ON)
option(BUILD_EXAMPLES "build the examples" ON)


set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_DEBUG_POSTFIX "d" CACHE STRING "add a postfix, usually d on windows")
set(CMAKE_RELEASE_POSTFIX "" CACHE STRING "add a postfix, usually empty on windows")
set(CMAKE_RELWITHDEBINFO_POSTFIX "rd" CACHE STRING "add a postfix, usually empty on windows")
set(CMAKE_MINSIZEREL_POSTFIX "s" CACHE STRING "add a postfix, usually empty on windows")
# Set the build postfix extension according to what configuration is being built.

if(CMAKE_BUILD_TYPE MATCHES "Release")
    set(CMAKE_BUILD_POSTFIX "${CMAKE_RELEASE_POSTFIX}")
	set(SARIBBON_BUILD_TYPE release)
elseif (CMAKE_BUILD_TYPE MATCHES "MinSizeRel")
    set(CMAKE_BUILD_POSTFIX "${CMAKE_MINSIZEREL_POSTFIX}")
	set(SARIBBON_BUILD_TYPE unknow)
	set(SARIBBON_BUILD_TYPE release)
elseif(CMAKE_BUILD_TYPE MATCHES "RelWithDebInfo")
    set(CMAKE_BUILD_POSTFIX "${CMAKE_RELWITHDEBINFO_POSTFIX}")
	set(SARIBBON_BUILD_TYPE release)
elseif(CMAKE_BUILD_TYPE MATCHES "Debug")
    set(CMAKE_BUILD_POSTFIX "${CMAKE_DEBUG_POSTFIX}")
	set(SARIBBON_BUILD_TYPE debug)#SARIBBON_BUILD_TYPE用于生成bin目录的后缀名称
else()
    set(CMAKE_BUILD_POSTFIX "")
	set(SARIBBON_BUILD_TYPE unknow)
endif()

if(MSVC)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} /wd4819")
endif()

# include(CPack)

message(STATUS "SARibbon ${SARIBBON_VERSION}")
# 平台判断
if("${CMAKE_SIZEOF_VOID_P}" STREQUAL "4")
    set(SARIBBON_PLATFORM "86")
else()
    set(SARIBBON_PLATFORM "64")
endif()

# load Qt library, minimum version required is 5.8
# cn:Qt库加载,最低版本要求为5.8

find_package(QT NAMES Qt6 Qt5 COMPONENTS Core REQUIRED)
find_package(Qt${QT_VERSION_MAJOR} 5.8 COMPONENTS Core Gui Widgets REQUIRED)
message(STATUS "current Qt version is Qt${QT_VERSION_MAJOR}.${QT_VERSION_MINOR}.${QT_VERSION_PATCH}")



include(cmake/WinResource.cmake)

# The bin file directory is one level above the current directory
# cn:bin文件目录在当前目录的上上一级
set(SARIBBON_BIN_NAME bin_qt${QT_VERSION_MAJOR}.${QT_VERSION_MINOR}.${QT_VERSION_PATCH}_${SARIBBON_BUILD_TYPE}_${SARIBBON_PLATFORM})
set(SARIBBON_BIN_DIR ${CMAKE_CURRENT_SOURCE_DIR}/${SARIBBON_BIN_NAME})


add_subdirectory(src)
add_subdirectory(cmake)
