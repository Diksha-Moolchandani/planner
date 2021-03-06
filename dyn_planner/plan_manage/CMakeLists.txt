cmake_minimum_required(VERSION 2.8.3)
project(plan_manage)

set(CMAKE_BUILD_TYPE "Release")
set(CMAKE_CXX_FLAGS "-std=c++11")
set(CMAKE_CXX_FLAGS_RELEASE "-O3 -Wall -g")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -DBOOST_MATH_DISABLE_FLOAT128")
find_package(Eigen3 REQUIRED)
find_package(PCL 1.7 REQUIRED)

find_package(catkin REQUIRED COMPONENTS
  roscpp
  std_msgs
  geometry_msgs
  quadrotor_msgs
  plan_env
  path_searching
  bspline_opt
  traj_utils
  message_generation
  mavros
  pcl_conversions
  pcl_ros
  sensor_msgs
  octomap_msgs
  octomap_ros
)

# Generate messages in the 'msg' folder
add_message_files(
  FILES
  Bspline.msg
  )

# Generate added messages and services with any dependencies listed here
generate_messages(
  DEPENDENCIES
  std_msgs
  geometry_msgs
)

catkin_package(
 INCLUDE_DIRS include
 LIBRARIES plan_manage
 CATKIN_DEPENDS plan_env path_searching bspline_opt traj_utils message_runtime
#  DEPENDS system_lib
)

include_directories(
  include
  SYSTEM
  ${catkin_INCLUDE_DIRS} ${PROJECT_SOURCE_DIR}/include
  ${EIGEN3_INCLUDE_DIR}
  ${PCL_INCLUDE_DIRS}
)

add_executable(dyn_planner_node
  src/dyn_planner_node.cpp 
  src/planning_fsm.cpp
  src/dyn_planner_manager.cpp
  )

target_link_libraries(dyn_planner_node ${catkin_LIBRARIES})

add_executable(dyn_planner_validator_node src/dyn_planner_validator_node.cpp)
target_link_libraries(dyn_planner_validator_node ${catkin_LIBRARIES})

add_executable(traj_server 
src/traj_server.cpp)
target_link_libraries(traj_server ${catkin_LIBRARIES})
add_dependencies(traj_server ${${PROJECT_NAME}_EXPORTED_TARGETS})

#add_library(plan_edt_map src/edtmap_wrapper.cpp)
#target_link_libraries(plan_edt_map ${catkin_LIBRARIES})  

#add_executable(edtmap_wrapper_node src/edtmap_wrapper_node.cpp src/edtmap_wrapper.cpp)
#target_link_libraries(edtmap_wrapper_node ${catkin_LIBRARIES})
#add_dependencies(edtmap_wrapper_node ${${PROJECT_NAME}_EXPORTED_TARGETS})
