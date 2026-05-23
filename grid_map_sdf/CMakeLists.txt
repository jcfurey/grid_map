cmake_minimum_required(VERSION 3.5)
project(grid_map_sdf)

find_package(ament_cmake_auto REQUIRED)
find_package(grid_map_cmake_helpers REQUIRED)
find_package(PCL REQUIRED COMPONENTS common)

find_package(Eigen3 QUIET)
if(NOT EIGEN3_FOUND)
  find_package(PkgConfig REQUIRED)
  pkg_check_modules(EIGEN3 REQUIRED eigen3)
  set(EIGEN3_INCLUDE_DIR ${EIGEN3_INCLUDE_DIRS})
endif()

ament_auto_find_build_dependencies()

grid_map_package()

include_directories(
  include
  SYSTEM
    ${EIGEN3_INCLUDE_DIR}
    ${PCL_INCLUDE_DIRS}
)

ament_auto_add_library(${PROJECT_NAME}
  src/SignedDistanceField.cpp
)

target_link_libraries(${PROJECT_NAME}
  ${PCL_LIBRARIES}
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

  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -pthread")
  ament_add_gtest(${PROJECT_NAME}-test
    test/SignedDistanceFieldTest.cpp
    test/test_grid_map_sdf.cpp)

  target_link_libraries(${PROJECT_NAME}-test ${PROJECT_NAME})
endif()

if(TARGET ${PROJECT_NAME}-test)
  target_link_libraries(${PROJECT_NAME}-test ${PROJECT_NAME})
endif()

ament_auto_package()
