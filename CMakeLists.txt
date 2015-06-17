cmake_minimum_required(VERSION 2.8)
project(uv)

set(TARGET_NAME "uv")

include(DetectOS)

if(NOT DETECTED_OS_WIN)
  add_definitions(-D_LARGEFILE_SOURCE -D_FILE_OFFSET_BITS=64)
  if(DETECTED_OS_SOLARIS)
    set(LIB_PTHREAD_NAME "pthread")
  elseif(NOT DETECTED_OS_SOLARIS AND NOT DETECTED_OS_ANDROID)
    set(LIB_PTHREAD_NAME "pthread")
  else()
    set(LIB_PTHREAD_NAME "")
  endif()
endif()

add_definitions(-D_GNU_SOURCE)

set(UV_INCLUDE "${CMAKE_CURRENT_LIST_DIR}/include" CACHE STRING "libuv include directory")
include_directories("${CMAKE_CURRENT_LIST_DIR}/include")
include_directories("${CMAKE_CURRENT_LIST_DIR}/src")

add_subdirectory("${CMAKE_CURRENT_LIST_DIR}/src" src)
