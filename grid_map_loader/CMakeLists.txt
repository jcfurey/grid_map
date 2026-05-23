cmake_minimum_required(VERSION 3.5)
project(grid_map_loader)

find_package(ament_cmake_auto REQUIRED)
find_package(grid_map_cmake_helpers REQUIRED)

ament_auto_find_build_dependencies()

grid_map_package()

include_directories(
  include
)

set(library_name ${PROJECT_NAME}_core)

ament_auto_add_library(${library_name} SHARED
  src/GridMapLoader.cpp
)

ament_auto_add_executable(${PROJECT_NAME}
  src/grid_map_loader_node.cpp
)

target_link_libraries(${PROJECT_NAME} ${library_name})

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
  ament_lint_auto_find_test_dependencies()
endif()

ament_auto_package()
