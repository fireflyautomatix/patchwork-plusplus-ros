cmake_minimum_required(VERSION 3.5)
project(patchworkpp)
set(CMAKE_BUILD_TYPE "Release")

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

add_definitions(-DTARGET_ROS2)
find_package(ament_cmake REQUIRED)
find_package(rclcpp REQUIRED)
find_package(PCL REQUIRED COMPONENTS common filters io kdtree)
find_package(pcl_conversions REQUIRED)
find_package(std_msgs REQUIRED)
find_package(sensor_msgs REQUIRED)
find_package(rclcpp_components REQUIRED)
find_package(Boost 1.54 REQUIRED)
find_package(visualization_msgs REQUIRED)

add_library(patchworkpp SHARED src/patchworkpp.cpp)
target_include_directories(patchworkpp
  PUBLIC
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
  $<INSTALL_INTERFACE:include>
)
ament_target_dependencies(patchworkpp
  rclcpp
  pcl_conversions
  std_msgs
  sensor_msgs
  rclcpp_components
  visualization_msgs
)
target_link_libraries(patchworkpp
  ${PCL_COMMON_LIBRARIES}
  ${PCL_FILTERS_LIBRARIES}
  ${PCL_IO_LIBRARIES}
  ${PCL_KDTREE_LIBRARIES}
)
rclcpp_components_register_node(patchworkpp
  PLUGIN "patchworkpp::PatchworkppPointXYZI"
  EXECUTABLE patchworkpp_point_xyzi
)

add_executable(demo src/demo.cpp)
ament_target_dependencies(demo
  pcl_conversions
  rclcpp
)
target_link_libraries(demo
  patchworkpp
)

install(TARGETS
  patchworkpp
  ARCHIVE DESTINATION lib
  LIBRARY DESTINATION lib
  RUNTIME DESTINATION bin
)

# ==== Install ====
install(TARGETS demo
ARCHIVE
LIBRARY
RUNTIME
DESTINATION lib/${PROJECT_NAME})

install(DIRECTORY include/${PROJECT_NAME}/
DESTINATION include/${PROJECT_NAME})

install(DIRECTORY launch config rviz DESTINATION share/${PROJECT_NAME})

ament_export_dependencies(ament_cmake)
ament_export_dependencies(rclcpp)
ament_export_dependencies(pcl_conversions)
ament_export_dependencies(std_msgs)
ament_export_dependencies(sensor_msgs)
ament_export_include_directories(${INCLUDE_DIRS})
ament_package()
