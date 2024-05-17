cmake_minimum_required(VERSION 3.0)

project(YAKL
        LANGUAGES CXX
        HOMEPAGE_URL https://github.com/mrnorman/Ensify/
        VERSION 0.1
        DESCRIPTION "Run many ensembles within a single MPI executable with ease")

add_library(Ensify INTERFACE)
target_include_directories(Ensify INTERFACE ${CMAKE_CURRENT_SOURCE_DIR})

if (NOT ENSIFY_EXCLUDE_YAML_CPP)
  set(YAML_CPP_BUILD_TOOLS   OFF CACHE BOOL "" FORCE)
  set(YAML_BUILD_SHARED_LIBS OFF CACHE BOOL "" FORCE)
  set(YAML_CPP_BUILD_CONTRIB OFF CACHE BOOL "" FORCE)
  add_subdirectory(${CMAKE_CURRENT_SOURCE_DIR}/../external/yaml-cpp ${CMAKE_CURRENT_BINARY_DIR}/yaml-cpp)
  target_include_directories(Ensify INTERFACE ${CMAKE_CURRENT_SOURCE_DIR}/yaml-cpp/include)
  target_link_libraries(Ensify INTERFACE yaml-cpp)
endif()


