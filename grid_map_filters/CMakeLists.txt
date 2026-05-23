cmake_minimum_required(VERSION 3.5)
project(grid_map_filters)

find_package(ament_cmake_auto REQUIRED)
find_package(grid_map_cmake_helpers REQUIRED)

find_package(PkgConfig REQUIRED)
pkg_check_modules(TBB REQUIRED tbb)

ament_auto_find_build_dependencies()

grid_map_package()

set(filter_libs
  threshold_filter
  min_in_radius_filter
  mean_in_radius_filter
  normal_vectors_filter
  curvature_filter
  normal_color_map_filter
  light_intensity_filter
  math_expression_filter
  sliding_window_math_expression_filter
  duplication_filter
  deletion_filter
  color_fill_filter
  color_map_filter
  color_blending_filter
  set_basic_layers_filter
  buffer_normalizer_filter
)

include_directories(
  include
  SYSTEM
    ${TBB_INCLUDE_DIRS}
)

ament_auto_add_library(threshold_filter SHARED src/ThresholdFilter.cpp)
ament_auto_add_library(min_in_radius_filter SHARED src/MinInRadiusFilter.cpp)
ament_auto_add_library(mean_in_radius_filter SHARED src/MeanInRadiusFilter.cpp)
ament_auto_add_library(normal_vectors_filter SHARED src/NormalVectorsFilter.cpp)
ament_auto_add_library(curvature_filter SHARED src/CurvatureFilter.cpp)
ament_auto_add_library(normal_color_map_filter SHARED src/NormalColorMapFilter.cpp)
ament_auto_add_library(light_intensity_filter SHARED src/LightIntensityFilter.cpp)
ament_auto_add_library(math_expression_filter SHARED src/MathExpressionFilter.cpp)
ament_auto_add_library(sliding_window_math_expression_filter SHARED src/SlidingWindowMathExpressionFilter.cpp)
ament_auto_add_library(duplication_filter SHARED src/DuplicationFilter.cpp)
ament_auto_add_library(deletion_filter SHARED src/DeletionFilter.cpp)
ament_auto_add_library(color_fill_filter SHARED src/ColorFillFilter.cpp)
ament_auto_add_library(color_map_filter SHARED src/ColorMapFilter.cpp)
ament_auto_add_library(color_blending_filter SHARED src/ColorBlendingFilter.cpp)
ament_auto_add_library(set_basic_layers_filter SHARED src/SetBasicLayersFilter.cpp)
ament_auto_add_library(buffer_normalizer_filter SHARED src/BufferNormalizerFilter.cpp)

foreach(lib_name ${filter_libs})
  target_link_libraries(${lib_name} ${TBB_LIBRARIES})
  if(CMAKE_CXX_COMPILER_ID MATCHES "GNU")
    target_compile_options(${lib_name} PRIVATE "SHELL:--param ggc-min-expand=1")
    target_compile_options(${lib_name} PRIVATE "SHELL:--param ggc-min-heapsize=32768")
  endif()
endforeach()

install(
  FILES filter_plugins.xml
  DESTINATION share/${PROJECT_NAME}
)

if(BUILD_TESTING)
  find_package(ament_lint_auto REQUIRED)
  find_package(ament_lint_auto QUIET)
  if(ament_lint_auto_FOUND)
    list(APPEND AMENT_LINT_AUTO_EXCLUDE
      ament_cmake_cpplint
      ament_cmake_copyright
      ament_cmake_uncrustify
    )
    ament_lint_auto_find_test_dependencies()

    find_package(ament_cmake_cpplint)
    ament_cpplint(
      FILTERS -legal/copyright -build/include_order
    )

    find_package(ament_cmake_uncrustify)
    set(
      _linter_excludes
      include/EigenLab/EigenLab.hpp
    )
    ament_uncrustify(
      EXCLUDE ${_linter_excludes}
      LANGUAGE c++
    )
  endif()
  ament_lint_auto_find_test_dependencies()
endif()

pluginlib_export_plugin_description_file(filters filter_plugins.xml)
ament_auto_package()
