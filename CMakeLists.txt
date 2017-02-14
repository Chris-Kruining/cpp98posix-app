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
  CMAKE_ARGS -DBuildShared=ON -DCMAKE_INSTALL_PREFIX=${GLOBAL_OUTPUT_PATH}/cpp98posix-framework

  TEST_COMMAND ""
)

ExternalProject_Add_Step(
  Framework CopyToBin
  COMMAND ${CMAKE_COMMAND} -E copy_directory ${GLOBAL_OUTPUT_PATH}/cpp98posix-framework/bin ${GLOBAL_OUTPUT_PATH}
  COMMAND ${CMAKE_COMMAND} -E copy_directory ${GLOBAL_OUTPUT_PATH}/cpp98posix-framework/lib ${GLOBAL_OUTPUT_PATH}
  DEPENDEES install
)

set(Framework_INCLUDE_DIRS "${CMAKE_SOURCE_DIR}/lib/cpp98posix-framework/src")
set(Framework_LIBRARIES "${CMAKE_SHARED_LIBRARY_PREFIX}cpp98posix-framework${CMAKE_SHARED_LIBRARY_SUFFIX}")
include_directories(${Framework_INCLUDE_DIRS})


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
