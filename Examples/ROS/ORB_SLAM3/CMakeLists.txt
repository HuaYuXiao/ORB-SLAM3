cmake_minimum_required(VERSION 2.4.6)
project(orb_slam3)

IF(NOT ROS_BUILD_TYPE)
  SET(ROS_BUILD_TYPE Release)
ENDIF()

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS}  -Wall  -O3 -march=native ")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall  -O3 -march=native")

# Check C++14 support
include(CheckCXXCompilerFlag)
CHECK_CXX_COMPILER_FLAG("-std=c++14" COMPILER_SUPPORTS_CXX14)
   set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++14")
   add_definitions(-DCOMPILEDWITHC14)

LIST(APPEND CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/../../../cmake_modules)

find_package(OpenCV 4.2 QUIET)
if(NOT OpenCV_FOUND)
	message(FATAL_ERROR "OpenCV >= 3.2 not found.")
endif()
find_package(Eigen3 3.1.0 REQUIRED)
find_package(Pangolin REQUIRED)
find_package(catkin REQUIRED COMPONENTS
        roscpp
        std_msgs
        geometry_msgs
        message_generation
        cv_bridge
        sensor_msgs
        rospy
        tf
        visualization_msgs
        nav_msgs
        pcl_ros
        prometheus_msgs
)

include_directories(
        include
        SYSTEM
        ${catkin_INCLUDE_DIRS}
        ${EIGEN3_INCLUDE_DIR}
        ${PCL_INCLUDE_DIRS}
        ${OpenCV_INCLUDE_DIRS}
${PROJECT_SOURCE_DIR}
${PROJECT_SOURCE_DIR}/../../../
${PROJECT_SOURCE_DIR}/../../../include
${PROJECT_SOURCE_DIR}/../../../include/CameraModels
${PROJECT_SOURCE_DIR}/../../../Thirdparty/Sophus
${Pangolin_INCLUDE_DIRS}
)

set(LIBS
        ${catkin_LIBRARIES}
${OpenCV_LIBS} 
${EIGEN3_LIBS}
${Pangolin_LIBRARIES}
${PROJECT_SOURCE_DIR}/../../../Thirdparty/DBoW2/lib/libDBoW2.so
${PROJECT_SOURCE_DIR}/../../../Thirdparty/g2o/lib/libg2o.so
${PROJECT_SOURCE_DIR}/../../../lib/liborb_slam3.so
-lboost_system
)

# Node for monocular camera
add_executable(Mono
src/ros_mono.cc
)
target_link_libraries(Mono
${LIBS}
)

# Node for monocular camera (Augmented Reality Demo)
add_executable(MonoAR
src/AR/ros_mono_ar.cc
src/AR/ViewerAR.h
src/AR/ViewerAR.cc
)
target_link_libraries(MonoAR
${LIBS}
)

# Node for stereo camera
add_executable(Stereo
src/ros_stereo.cc
)
target_link_libraries(Stereo
${LIBS}
)

# Node for RGB-D camera
add_executable(RGBD
src/ros_rgbd.cc
)
target_link_libraries(RGBD
${LIBS}
)

# Node for monocular-inertial camera
add_executable(Mono_Inertial
src/ros_mono_inertial.cc
)
target_link_libraries(Mono_Inertial
${LIBS}
)

# Node for stereo-inertial camera
add_executable(Stereo_Inertial
src/ros_stereo_inertial.cc
)
target_link_libraries(Stereo_Inertial
${LIBS}
)
