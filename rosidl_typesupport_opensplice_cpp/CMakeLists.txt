cmake_minimum_required(VERSION 3.5)

project(rosidl_typesupport_opensplice_cpp)

if(NOT WIN32)
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++14 -Wall -Wextra -Wpedantic")
endif()

find_package(ament_cmake REQUIRED)

find_package(opensplice_cmake_module REQUIRED)
find_package(OpenSplice MODULE)
if(NOT OpenSplice_FOUND)
  ament_package()
  message(WARNING "Could not find PrismTech OpenSplice - skipping '${PROJECT_NAME}'")
  return()
endif()

find_package(ament_cmake_python REQUIRED)

ament_export_dependencies(rmw)
ament_export_dependencies(rosidl_cmake)
ament_export_dependencies(rosidl_generator_c)
ament_export_dependencies(rosidl_generator_cpp)
ament_export_dependencies(rosidl_generator_dds_idl)
ament_export_dependencies(rosidl_typesupport_interface)

ament_export_include_directories(include)

ament_python_install_package(${PROJECT_NAME})

add_library(${PROJECT_NAME} SHARED src/identifier.cpp)
if(WIN32)
  target_compile_definitions(${PROJECT_NAME}
    PRIVATE "ROSIDL_TYPESUPPORT_OPENSPLICE_CPP_BUILDING_DLL")
endif()
target_include_directories(${PROJECT_NAME}
  PUBLIC
  include
)
ament_export_libraries(${PROJECT_NAME})

ament_index_register_resource("rosidl_typesupport_cpp")

if(BUILD_TESTING)
  find_package(ament_lint_auto REQUIRED)
  ament_lint_auto_find_test_dependencies()
endif()

ament_package(
  CONFIG_EXTRAS "rosidl_typesupport_opensplice_cpp-extras.cmake.in"
)

install(
  PROGRAMS bin/rosidl_typesupport_opensplice_cpp
  DESTINATION lib/rosidl_typesupport_opensplice_cpp
)
install(
  DIRECTORY cmake resource
  DESTINATION share/${PROJECT_NAME}
)
install(
  DIRECTORY include/
  DESTINATION include
)
install(
  TARGETS ${PROJECT_NAME}
  ARCHIVE DESTINATION lib
  LIBRARY DESTINATION lib
  RUNTIME DESTINATION bin
)
