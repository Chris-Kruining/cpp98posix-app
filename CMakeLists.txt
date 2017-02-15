cmake_minimum_required(VERSION 2.8)

set(project_name app)
set(project_name_test ${project_name}_test)
set(CMAKE_BUILD_TYPE Release)

project(${project_name})

include(ExternalProject)

ExternalProject_Add(
  Framework

  GIT_REPOSITORY "https://github.com/Chris-Kruining/cpp98posix-framework.git"
  GIT_TAG "develop"

  UPDATE_COMMAND ""
  PATCH_COMMAND ""

  SOURCE_DIR "${CMAKE_SOURCE_DIR}/lib/cpp98posix-framework"
  CMAKE_ARGS -DBuildShared=ON

  TEST_COMMAND ""
)

include_directories(${CMAKE_SOURCE_DIR}/lib/cpp98posix-framework/src)

if ("${CMAKE_CXX_COMPILER_ID}" STREQUAL "Clang")
    add_definitions(-std=c++98 -stdlib=libc++ -O3 -Wall)
elseif ("${CMAKE_CXX_COMPILER_ID}" STREQUAL "GNU")
    add_definitions(-std=c++98 -O3 -Wall)
endif()

include_directories(src)

add_executable(${project_name} src/main.cpp ${src})

target_link_libraries(${project_name} pthread)

include_directories(lib/catch)

enable_testing()

add_executable(${project_name_test} test/main.cpp)
target_link_libraries(${project_name_test} ${project_name_lib} pthread)

add_test(Utilities::String ${project_name_test})
