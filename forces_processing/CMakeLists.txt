cmake_minimum_required(VERSION 2.8.3)
project(forces_processing)

add_compile_options(-std=c++11)

find_package(catkin REQUIRED COMPONENTS
        moveit_core
        moveit_kinematics
        roscpp
        rospy
        std_msgs
        contact_control
        ur5_moveit
        cartesian_impedance_msgs)

## System dependencies are found with CMake's conventions
find_package(Eigen3 REQUIRED)

catkin_install_python(PROGRAMS
        scripts/force_reading_test.py
        DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}/src
        )


catkin_package(
        INCLUDE_DIRS include
        LIBRARIES forces_processing
        CATKIN_DEPENDS contact_control ur5_moveit moveit_core moveit_kinematics
        #  DEPENDS system_lib
)


include_directories(
        include
        ${catkin_INCLUDE_DIRS}
        ${Eigen_INCLUDE_DIRS}
        ${contact_control_INCLUDE_DIRS}
)

#

add_library(forces_processing src/particle_filter.cpp src/particle_filter_node.cpp)
target_link_libraries(forces_processing ${catkin_LIBRARIES} contact_control ur5_moveit)

add_executable(particle_filter_node src/particle_filter_node.cpp)
target_link_libraries(particle_filter_node ${catkin_LIBRARIES} forces_processing)
add_dependencies(particle_filter_node ${catkin_EXPORTED_TARGETS})


install(FILES
        launch/force_reading_test.launch
        DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}/launch
        )