cmake_minimum_required(VERSION 2.8.12)
project(uv)

add_subdirectory(src)
if(BUILD_TESTS)
  add_subdirectory(test)
endif()
