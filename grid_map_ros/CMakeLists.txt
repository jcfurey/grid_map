cmake_minimum_required(VERSION 3.5)
project(grid_map_ros)

find_package(ament_cmake_auto REQUIRED)
find_package(grid_map_cmake_helpers REQUIRED)

ament_auto_find_build_dependencies()

grid_map_package()

include_directories(
  include
  SYSTEM
    ${EIGEN3_INCLUDE_DIR}
)

ament_auto_add_library(${PROJECT_NAME} SHARED
  src/GridMapRosConverter.cpp
  src/GridMapMsgHelpers.cpp
  src/PolygonRosConverter.cpp
)

target_include_directories(${PROJECT_NAME} SYSTEM PUBLIC
  ${grid_map_cv_INCLUDE_DIRS}
)

target_link_libraries(${PROJECT_NAME}
  ${grid_map_cv_LIBRARIES}
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

  ament_add_gtest(${PROJECT_NAME}-test
    test/test_grid_map_ros.cpp
    test/GridMapRosTest.cpp
  )

  file(COPY ${CMAKE_CURRENT_SOURCE_DIR}/resource/double_chatter DESTINATION ${CMAKE_CURRENT_BINARY_DIR})
  file(COPY ${CMAKE_CURRENT_SOURCE_DIR}/resource/test_multitopic DESTINATION ${CMAKE_CURRENT_BINARY_DIR})
endif()

if(TARGET ${PROJECT_NAME}-test)
  target_link_libraries(${PROJECT_NAME}-test ${PROJECT_NAME})
endif()

ament_auto_package()
