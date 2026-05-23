cmake_minimum_required(VERSION 3.5)
project(grid_map_rviz_plugin)

find_package(ament_cmake_auto REQUIRED)
find_package(grid_map_cmake_helpers REQUIRED)
find_package(Qt6 REQUIRED COMPONENTS Core Widgets)

ament_auto_find_build_dependencies()

grid_map_package()

## Qt boilerplate options
set(CMAKE_INCLUDE_CURRENT_DIR ON)
set(CMAKE_AUTOMOC ON)

include_directories(
  include
)

set(INCLUDE_FILES_QT
  include/grid_map_rviz_plugin/GridMapDisplay.hpp
)

qt6_wrap_cpp(MOC_FILES ${INCLUDE_FILES_QT})

## Avoid Qt signals and slots defining "emit", "slots", etc.
add_definitions(-DQT_NO_KEYWORDS)

set(SOURCE_FILES
  src/GridMapDisplay.cpp
  src/GridMapVisual.cpp
)

ament_auto_add_library(${PROJECT_NAME} SHARED
  ${SOURCE_FILES}
  ${MOC_FILES}
)

target_link_libraries(${PROJECT_NAME}
  Qt6::Core
  Qt6::Widgets
)

target_compile_definitions(${PROJECT_NAME} PRIVATE "RVIZ_DEFAULT_PLUGINS_BUILDING_LIBRARY")
target_compile_definitions(${PROJECT_NAME} PUBLIC "PLUGINLIB__DISABLE_BOOST_FUNCTIONS")

pluginlib_export_plugin_description_file(rviz_common plugin_description.xml)

install(
  FILES plugin_description.xml
  DESTINATION share/${PROJECT_NAME}
)

install(
  DIRECTORY icons
  DESTINATION share/${PROJECT_NAME}/icons
)

install(
  DIRECTORY doc
  DESTINATION share/${PROJECT_NAME}
)

if(BUILD_TESTING)
  find_package(ament_lint_auto REQUIRED)
  find_package(ament_lint_auto QUIET)
  if(ament_lint_auto_FOUND)
    list(APPEND AMENT_LINT_AUTO_EXCLUDE
      ament_cmake_cpplint
      ament_cmake_copyright
    )
    ament_lint_auto_find_test_dependencies()

    find_package(ament_cmake_cpplint)
    ament_cpplint(
      FILTERS -legal/copyright -build/include_order
    )
  endif()
endif()

ament_auto_package()
