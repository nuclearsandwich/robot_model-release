cmake_minimum_required(VERSION 3.5)

project(kdl_parser)

find_package(ament_cmake REQUIRED)
find_package(orocos_kdl REQUIRED)
find_package(tinyxml_vendor REQUIRED)
find_package(TinyXML REQUIRED)
find_package(urdf REQUIRED)
find_package(urdfdom_headers REQUIRED)
find_library(KDL_LIBRARY REQUIRED
  NAMES orocos-kdl
  HINTS ${orocos_kdl_LIBRARY_DIRS})

include_directories(
  include
  ${orocos_kdl_INCLUDE_DIRS}
  ${urdf_INCLUDE_DIRS}
  ${urdfdom_headers_INCLUDE_DIRS}
  ${TinyXML_INCLUDE_DIRS})

link_directories(
  ${orocos_kdl_LIBRARY_DIRS}
  ${urdf_LIBRARY_DIRS})

# Default to C++14
if(NOT CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 14)
endif()

add_library(
  ${PROJECT_NAME}
  SHARED
  src/kdl_parser.cpp)
target_link_libraries(
  ${PROJECT_NAME}
  ${TinyXML_LIBRARIES}
  ${orocos_kdl_LIBRARIES}
  ${urdf_LIBRARIES})

if(WIN32)
  target_compile_definitions(${PROJECT_NAME} PRIVATE "KDL_PARSER_BUILDING_DLL")
endif()

install(
  TARGETS ${PROJECT_NAME}
  LIBRARY DESTINATION lib
  ARCHIVE DESTINATION lib
  RUNTIME DESTINATION bin)

install(
  DIRECTORY include/${PROJECT_NAME}/
  DESTINATION include/${PROJECT_NAME})

if(BUILD_TESTING)
  find_package(ament_cmake_cppcheck REQUIRED)
  find_package(ament_cmake_cpplint REQUIRED)
  find_package(ament_cmake_lint_cmake REQUIRED)
  find_package(ament_cmake_uncrustify REQUIRED)
  ament_cppcheck()
  ament_cpplint()
  ament_lint_cmake()
  ament_uncrustify()
endif()

ament_export_dependencies(orocos_kdl)
ament_export_include_directories(include)
ament_export_libraries(${PROJECT_NAME})
ament_package()
