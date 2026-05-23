cmake_minimum_required(VERSION 3.5)
project(grid_map_octomap)

find_package(ament_cmake_auto REQUIRED)
find_package(grid_map_cmake_helpers REQUIRED)
find_package(OCTOMAP REQUIRED)

ament_auto_find_build_dependencies()

message(STATUS "Found Octomap (version ${octomap_VERSION}): ${OCTOMAP_INCLUDE_DIRS}")

include_directories(include)

ament_auto_add_library(${PROJECT_NAME}
  src/GridMapOctomapConverter.cpp
)

target_include_directories(${PROJECT_NAME} SYSTEM PUBLIC
  ${OCTOMAP_INCLUDE_DIRS}
)

target_link_libraries(${PROJECT_NAME}
  ${OCTOMAP_LIBRARIES}
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
    test/test_grid_map_octomap.cpp
    test/OctomapConverterTest.cpp
  )
endif()

if(TARGET ${PROJECT_NAME}-test)
  target_link_libraries(${PROJECT_NAME}-test
    ${PROJECT_NAME}
    ${OCTOMAP_LIBRARIES}
  )
endif()

ament_auto_package()
