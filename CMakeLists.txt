cmake_minimum_required(VERSION 3.22)
project(lab_sim)

find_package(ament_cmake REQUIRED)

find_package(example_interfaces REQUIRED)
find_package(moveit_studio_behavior_interface REQUIRED)
find_package(picknik_accessories REQUIRED)
find_package(pluginlib REQUIRED)

# Install all XML files in directory
set(PICKNIK_ACCESSORIES_SHARE_DIR
"${CMAKE_INSTALL_PREFIX}/../picknik_accessories/share/picknik_accessories/mujoco_assets/"
)
# Destination directory
set(DEST_DIR "${CMAKE_INSTALL_PREFIX}/share/${PROJECT_NAME}/description/")

set(THIS_PACKAGE_INCLUDE_DEPENDS moveit_studio_behavior_interface pluginlib
                                 example_interfaces)

install(DIRECTORY "${PICKNIK_ACCESSORIES_SHARE_DIR}"
        DESTINATION "${DEST_DIR}"
        FILES_MATCHING PATTERN "*")

add_library(
  example_behaviors SHARED
  src/hello_world.cpp
  src/delayed_message.cpp
  src/setup_mtc_wave_hand.cpp
  src/register_behaviors.cpp
  src/add_two_ints_service_client.cpp
  src/fibonacci_action_client.cpp
  src/get_string_from_topic.cpp
  src/publish_color_rgba.cpp
  src/setup_mtc_pick_from_pose.cpp
  src/setup_mtc_place_from_pose.cpp)
target_include_directories(
  example_behaviors
  PUBLIC $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
         $<INSTALL_INTERFACE:include>)
ament_target_dependencies(example_behaviors ${THIS_PACKAGE_INCLUDE_DEPENDS})

# Install individual XML files
# set(EXTERNAL_XML_FILES
#   "${PICKNIK_ACCESSORIES_SHARE_DIR}/ur5e/ur5e_globals.xml"
#   "${PICKNIK_ACCESSORIES_SHARE_DIR}/ur5e/ur5e.xml"
#   # Add additional files here if desired
# )
# foreach(xml_file IN LISTS EXTERNAL_XML_FILES)
#   install(FILES "${xml_file}"
#           DESTINATION "${DEST_DIR}"
#   )
# endforeach()

# Install Libraries
install(
  TARGETS example_behaviors
  EXPORT example_behaviorsTargets
  ARCHIVE DESTINATION lib
  LIBRARY DESTINATION lib
  RUNTIME DESTINATION bin
  INCLUDES
  DESTINATION include)

install(
  DIRECTORY
    config
    description
    launch
    objectives
    waypoints
  DESTINATION
    share/${PROJECT_NAME}
)

if(BUILD_TESTING)
  find_package(ament_lint_auto REQUIRED)
  ament_lint_auto_find_test_dependencies()
endif()

# Export the behavior plugins defined in this package so they are available to
# plugin loaders that load the behavior base class library from the
# moveit_studio_behavior package.
pluginlib_export_plugin_description_file(
  moveit_studio_behavior_interface example_behaviors_plugin_description.xml)

ament_export_targets(example_behaviorsTargets HAS_LIBRARY_TARGET)
ament_export_dependencies(${THIS_PACKAGE_INCLUDE_DEPENDS})
ament_package()
