cmake_minimum_required(VERSION 2.8)
project(uv)

set(TARGET_NAME "uv")

if(NOT WIN32)
  add_definitions(-D_LARGEFILE_SOURCE -D_FILE_OFFSET_BITS=64 -D__USE_FILE_OFFSET64)
  add_definitions(-D_GNU_SOURCE)
endif()

include_directories(${CMAKE_CURRENT_LIST_DIR}/include)
include_directories(${CMAKE_CURRENT_LIST_DIR}/src)

add_subdirectory(${CMAKE_CURRENT_LIST_DIR}/src src)
