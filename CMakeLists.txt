cmake_minimum_required(VERSION 2.8.12)
project(uv)

set(TARGET_NAME "uv")

include(DetectOS)

add_subdirectory("${CMAKE_CURRENT_LIST_DIR}/src" src)
if(BUILD_TESTS)
  add_subdirectory("${CMAKE_CURRENT_LIST_DIR}/test" test)
endif()
