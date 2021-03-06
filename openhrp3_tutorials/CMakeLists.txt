cmake_minimum_required(VERSION 2.4.6)
include($ENV{ROS_ROOT}/core/rosbuild/rosbuild.cmake)

rosbuild_init()

# Set the build type.  Options are:
#  Coverage       : w/ debug symbols, w/o optimization, w/ code-coverage
#  Debug          : w/ debug symbols, w/o optimization
#  Release        : w/o debug symbols, w/ optimization
#  RelWithDebInfo : w/ debug symbols, w/ optimization
#  MinSizeRel     : w/o debug symbols, w/ optimization, stripped binaries
#set(ROS_BUILD_TYPE RelWithDebInfo)

#set the default path for built executables to the "bin" directory
set(EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin)
#set the default path for built libraries to the "lib" directory
set(LIBRARY_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/lib)


rosbuild_find_ros_package("jsk_tools")
execute_process(COMMAND cmake -E chdir ${PROJECT_SOURCE_DIR} python ${jsk_tools_PACKAGE_PATH}/bin/launchdoc-generator.py ${PROJECT_NAME} --output_dir=./build/ --nomakefile RESULT_VARIABLE _make_failed)


## test code
# rosbuild_check_for_display(disp)
# if(disp)
#   rosbuild_add_rostest(launch/sample.launch)
#   rosbuild_add_rostest(launch/falling-box.launch)
#   rosbuild_add_rostest(launch/pa10.launch)
#   rosbuild_add_rostest(launch/sample-vehicle.launch)
#   rosbuild_add_rostest(launch/sample-rangesensor.launch)
#   rosbuild_add_rostest(launch/samplerobot-inhouse.launch)
#   rosbuild_add_rostest(launch/samplerobot-pickupbox.launch)
#   rosbuild_add_rostest(launch/samplerobot-walk.launch)
# endif(disp)


add_custom_command(OUTPUT ${PROJECT_SOURCE_DIR}/test/.gen_testcode
  COMMAND make -C ${PROJECT_SOURCE_DIR}/test/
  VERBATIM)
add_custom_target(gen_testcode DEPENDS test/barrett-wam.zae ${PROJECT_SOURCE_DIR}/test/.gen_testcode)
add_dependencies(tests gen_testcode)
rosbuild_download_test_data(https://openrave.svn.sourceforge.net/svnroot/openrave/data/robots/barrett-wam.zae test/barrett-wam.zae 7d59cad7186b3fb516c3dd75303c52f2)
rosbuild_add_rostest(test/test-modelloader.launch)
