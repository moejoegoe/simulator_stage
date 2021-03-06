cmake_minimum_required(VERSION 2.4.6)
include($ENV{ROS_ROOT}/core/rosbuild/rosbuild.cmake)
include(FindPkgConfig)
rosbuild_init()

rosbuild_add_boost_directories()

# Build Stage
execute_process(COMMAND cmake -E chdir ${PROJECT_SOURCE_DIR} make -f Makefile.stage
                RESULT_VARIABLE _make_failed)
if(_make_failed)
  message(FATAL_ERROR "Build of Stage failed")
endif(_make_failed)

# Find the build flags for Stage
set(ENV{PKG_CONFIG_PATH} "${PROJECT_SOURCE_DIR}/lib/pkgconfig:$ENV{PKG_CONFIG_PATH}")
pkg_check_modules(STAGE REQUIRED stage)

include_directories(${STAGE_INCLUDE_DIRS})
link_directories(${STAGE_LIBRARY_DIRS})

rosbuild_add_executable(bin/stageros src/stageros.cpp)
rosbuild_link_boost(bin/stageros thread)
add_dependencies(bin/stageros build_stage)

rosbuild_add_compile_flags(bin/stageros ${STAGE_CFLAGS_OTHERS})
target_link_libraries(bin/stageros ${STAGE_LIBRARIES})
rosbuild_add_link_flags(bin/stageros ${STAGE_LDFLAGS_OTHERS})


rosbuild_add_rostest(test/hztest.xml)
