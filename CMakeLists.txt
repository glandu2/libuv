cmake_minimum_required(VERSION 2.8)
project(libuv)

include_directories(${CMAKE_CURRENT_LIST_DIR}/include)
include_directories(${CMAKE_CURRENT_LIST_DIR}/src)

add_subdirectory(${CMAKE_CURRENT_LIST_DIR}/src src)
