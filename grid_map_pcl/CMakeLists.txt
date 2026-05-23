cmake_minimum_required(VERSION 3.5)
project(grid_map_pcl)

find_package(ament_cmake_auto REQUIRED)
find_package(grid_map_cmake_helpers REQUIRED)
find_package(PCL REQUIRED)

find_package(OpenMP QUIET)
if(OpenMP_FOUND)
  add_compile_options("${OpenMP_CXX_FLAGS}")
  add_definitions(-DGRID_MAP_PCL_OPENMP_FOUND=${OpenMP_FOUND})
endif()

ament_auto_find_build_dependencies()

include_directories(${PCL_INCLUDE_DIRS})
link_directories(${PCL_LIBRARY_DIRS})
add_definitions(${PCL_DEFINITIONS})

grid_map_package()

set(SRC_FILES
  src/GridMapPclLoader.cpp
  src/PclLoaderParameters.cpp
  src/PointcloudProcessor.cpp
  src/helpers.cpp
  src/GridMapPclConverter.cpp
)

include_directories(
  include
  SYSTEM
    ${EIGEN3_INCLUDE_DIR}
    ${OpenMP_CXX_INCLUDE_DIRS}
)

ament_auto_add_library(${PROJECT_NAME} SHARED
  ${SRC_FILES}
)

target_link_libraries(${PROJECT_NAME}
  ${OpenMP_CXX_LIBRARIES}
  ${PCL_LIBRARIES}
  yaml-cpp
)

ament_auto_add_executable(grid_map_pcl_loader_node
  src/grid_map_pcl_loader_node.cpp
)

target_link_libraries(grid_map_pcl_loader_node
  ${PROJECT_NAME}
  ${OpenMP_CXX_LIBRARIES}
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

  find_package(ament_cmake_gtest REQUIRED)

  install(
    DIRECTORY test/test_data/
    DESTINATION share/${PROJECT_NAME}/test_data/
  )

  ament_add_gtest(${PROJECT_NAME}-test
    test/GridMapPclLoaderTest.cpp
    test/HelpersTest.cpp
    test/PointcloudCreator.cpp
    test/PointcloudProcessorTest.cpp
    test/test_helpers.cpp
    test/test_grid_map_pcl.cpp
    TIMEOUT 300
  )
endif()

if(TARGET ${PROJECT_NAME}-test)
  target_link_libraries(${PROJECT_NAME}-test
    ${PROJECT_NAME}
  )
endif()

ament_auto_package()
