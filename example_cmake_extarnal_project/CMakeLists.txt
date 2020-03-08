project(tbb-dependency)
cmake_minimum_required(VERSION 3.14)

include(ExternalProject)

set(OFFLINE 0 CACHE BOOL "Skips download and uses local git repo from (https://github.com/kubaroth/tbb.git)
  branch: 2019-make-install
  cloned into root/build/tbb_src")

# TODO: skip entirely if TBB found using Find
if(OFFLINE EQUAL 0)
  set (TBB_REPO GIT_REPOSITORY https://github.com/kubaroth/tbb.git)
else()
  set (TBB_REPO GIT_REPOSITORY ${CMAKE_CURRENT_SOURCE_DIR}/tbb_src) # Use local repo
endif()

message (STATUS "Using TBB sources in: ${TBB_REPO}")

# TBB currently uses in-source build using Makefile
# Out of the box it does not ship with 'install' target
# Forked version kubaroth contains necessary changes.
ExternalProject_Add(
  "TBB"
  PREFIX "external"
  # GIT_REPOSITORY https://github.com/kubaroth/tbb.git
  ${TBB_REPO}
  GIT_TAG "2019-make-install"  # TODO - update to 2020 with termux branch
  SOURCE_DIR ${CMAKE_BINARY_DIR}/tbb_src
  # no configuration required but still needed if cmake is not used by the build
  UPDATE_COMMAND "" # faster subsequent runs
  CONFIGURE_COMMAND ""
  BUILD_IN_SOURCE ON
  # Already installed into desired final location, TODO: make it configurable with CMAKE_INSTALL_PREFIX
  BUILD_COMMAND tbb_install_prefix=${CMAKE_CURRENT_SOURCE_DIR}/install_dir $(MAKE) install
)

# sources
add_executable(example main.cpp)
add_dependencies(example TBB)
target_link_directories(example BEFORE PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/install_dir/lib)
target_include_directories(example BEFORE PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/install_dir/include)
# target_link_libraries(example -L/${CMAKE_CURRENT_SOURCE_DIR}/install_dir/lib/)
target_link_libraries(example tbb)
install(TARGETS example DESTINATION ${CMAKE_CURRENT_SOURCE_DIR}/install_dir/bin)
