cmake_minimum_required(VERSION 2.8.12)
project(moveit_ros_perception)

add_compile_options(-std=c++11)

if(NOT CMAKE_CONFIGURATION_TYPES AND NOT CMAKE_BUILD_TYPE)
  set(CMAKE_BUILD_TYPE Release)
endif()

find_package(Boost REQUIRED thread signals)

find_package(OpenGL REQUIRED)
find_package(GLEW REQUIRED)
find_package(GLUT REQUIRED)
if (OPENGL_FOUND)
  set(gl_LIBS ${gl_LIBS} ${OPENGL_LIBRARIES})
endif(OPENGL_FOUND)
find_package(X11 REQUIRED)

find_package(catkin REQUIRED COMPONENTS
  moveit_core
  roscpp
  rosconsole
  urdf
  message_filters
  tf
  tf_conversions
  pluginlib
  image_transport
  object_recognition_msgs
  sensor_msgs
  moveit_msgs
)

find_package(Eigen3 REQUIRED)

# Eigen 3.2 (Wily) only provides EIGEN3_INCLUDE_DIR, not EIGEN3_INCLUDE_DIRS
if(NOT EIGEN3_INCLUDE_DIRS)
  set(EIGEN3_INCLUDE_DIRS ${EIGEN3_INCLUDE_DIR})
endif()

find_package(octomap REQUIRED)
find_package(OpenCV REQUIRED)

catkin_package(
  INCLUDE_DIRS
    mesh_filter/include
    lazy_free_space_updater/include
    depth_image_octomap_updater/include
    point_containment_filter/include
    occupancy_map_monitor/include
    pointcloud_octomap_updater/include
    semantic_world/include
    ${OCTOMAP_INCLUDE_DIRS}
  LIBRARIES
    moveit_lazy_free_space_updater
    moveit_point_containment_filter
    moveit_occupancy_map_monitor
    moveit_pointcloud_octomap_updater_core
    moveit_semantic_world
    ${OCTOMAP_LIBRARIES}
  CATKIN_DEPENDS
    moveit_core
    image_transport
    moveit_msgs
  DEPENDS
    EIGEN3
)

include_directories(mesh_filter/include
                    lazy_free_space_updater/include
                    depth_image_octomap_updater/include
                    point_containment_filter/include
                    occupancy_map_monitor/include
                    pointcloud_octomap_updater/include
                    semantic_world/include
                    ${Boost_INCLUDE_DIRS}
                    ${catkin_INCLUDE_DIRS}
                    )
include_directories(SYSTEM
                    ${OpenCV_INCLUDE_DIRS}
                    ${EIGEN3_INCLUDE_DIRS}
                    ${GLEW_INCLUDE_DIR}
                    ${GLUT_INCLUDE_DIR}
                    ${X11_INCLUDE_DIR}
                    )

link_directories(${Boost_LIBRARY_DIRS})
link_directories(${catkin_LIBRARY_DIRS})
link_directories(${X11_LIBRARY_DIR})

add_subdirectory(lazy_free_space_updater)
add_subdirectory(point_containment_filter)
add_subdirectory(occupancy_map_monitor)
add_subdirectory(pointcloud_octomap_updater)
add_subdirectory(mesh_filter)
add_subdirectory(depth_image_octomap_updater)

add_subdirectory(semantic_world)

install(
  FILES
    pointcloud_octomap_updater_plugin_description.xml
    depth_image_octomap_updater_plugin_description.xml
  DESTINATION
    ${CATKIN_PACKAGE_SHARE_DESTINATION})
