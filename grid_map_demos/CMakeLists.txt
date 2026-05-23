cmake_minimum_required(VERSION 3.5.0)
project(grid_map_demos)

find_package(ament_cmake_auto REQUIRED)
find_package(grid_map_cmake_helpers REQUIRED)
find_package(OCTOMAP REQUIRED)

find_package(OpenCV REQUIRED
  COMPONENTS
    opencv_highgui
  CONFIG
)

ament_auto_find_build_dependencies()

grid_map_package()

include_directories(
  include
  SYSTEM
    ${EIGEN3_INCLUDE_DIR}
)

ament_auto_add_executable(simple_demo src/simple_demo_node.cpp)
ament_auto_add_executable(tutorial_demo src/tutorial_demo_node.cpp)
ament_auto_add_executable(iterators_demo
  src/iterators_demo_node.cpp
  src/IteratorsDemo.cpp
)
ament_auto_add_executable(image_to_gridmap_demo
  src/image_to_gridmap_demo_node.cpp
  src/ImageToGridmapDemo.cpp
)
ament_auto_add_executable(octomap_to_gridmap_demo
  src/octomap_to_gridmap_demo_node.cpp
  src/OctomapToGridmapDemo.cpp
)
ament_auto_add_executable(move_demo src/move_demo_node.cpp)
ament_auto_add_executable(iterator_benchmark src/iterator_benchmark.cpp)
ament_auto_add_executable(opencv_demo src/opencv_demo_node.cpp)
ament_auto_add_executable(resolution_change_demo src/resolution_change_demo_node.cpp)
ament_auto_add_executable(filters_demo src/filters_demo_node.cpp)
ament_auto_add_library(filters_demo_lib SHARED src/FiltersDemo.cpp)
ament_auto_add_executable(normal_filter_comparison_demo src/normal_filter_comparison_node.cpp)
ament_auto_add_executable(interpolation_demo
  src/interpolation_demo_node.cpp
  src/InterpolationDemo.cpp
)

target_link_libraries(octomap_to_gridmap_demo ${OCTOMAP_LIBRARIES})
target_link_libraries(filters_demo filters_demo_lib)

set(targets_list
  filters_demo
  filters_demo_lib
  image_to_gridmap_demo
  interpolation_demo
  iterator_benchmark
  iterators_demo
  move_demo
  normal_filter_comparison_demo
  octomap_to_gridmap_demo
  opencv_demo
  resolution_change_demo
  simple_demo
  tutorial_demo
)

foreach(target ${targets_list})
  if(CMAKE_CXX_COMPILER_ID MATCHES "GNU")
    target_compile_options(${target} PRIVATE "SHELL:--param ggc-min-expand=1")
    target_compile_options(${target} PRIVATE "SHELL:--param ggc-min-heapsize=32768")
  endif()
  target_compile_options(${target} PRIVATE
    -Wno-error=deprecated-declarations
    -Wno-error=cpp
  )
endforeach()

install(
  DIRECTORY config data doc launch rviz
  DESTINATION share/${PROJECT_NAME}
)

install(
  PROGRAMS scripts/image_publisher.py
  DESTINATION lib/${PROJECT_NAME}
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
  ament_lint_auto_find_test_dependencies()
endif()

ament_auto_package()
