cmake_minimum_required(VERSION 2.8.12)
project(uv)

add_subdirectory("${CMAKE_CURRENT_LIST_DIR}/src" src)
if(BUILD_TESTS)
  add_subdirectory("${CMAKE_CURRENT_LIST_DIR}/test" test)
endif()
