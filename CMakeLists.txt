cmake_minimum_required(VERSION 3.8)
project(custom_pkg)

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

# FIND dependencies
find_package(ament_cmake REQUIRED)
find_package(rclcpp REQUIRED)
find_package(std_msgs REQUIRED)
find_package(geometry_msgs REQUIRED)

# BUILD
add_executable(custom_pub_standalone src/custom_pub_standalone.cpp)
ament_target_dependencies(custom_pub_standalone rclcpp std_msgs)
# target_include_directories(custom_pub_standalone PUBLIC
#   $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
#   $<INSTALL_INTERFACE:include>)
target_compile_features(custom_pub_standalone PUBLIC c_std_99 cxx_std_17)  # Require C99 and C++17

add_library(custom_pub_lib src/custom_pub_lib.cpp)
ament_target_dependencies(custom_pub_lib rclcpp std_msgs)
target_include_directories(custom_pub_lib PUBLIC
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
#  $<INSTALL_INTERFACE:include>)
  $<INSTALL_INTERFACE:include/${PROJECT_NAME}>)
target_compile_features(custom_pub_lib PUBLIC c_std_99 cxx_std_17)  # Require C99 and C++17

add_executable(custom_pub_main src/custom_pub_main.cpp)
ament_target_dependencies(custom_pub_main rclcpp)
target_link_libraries(custom_pub_main custom_pub_lib)
target_compile_features(custom_pub_main PUBLIC c_std_99 cxx_std_17)  # Require C99 and C++17

add_executable(custom_sub_standalone src/custom_sub_standalone.cpp)
ament_target_dependencies(custom_sub_standalone rclcpp std_msgs)

# INSTALL
install(TARGETS 
  custom_pub_standalone
  custom_pub_main
  custom_sub_standalone
  DESTINATION lib/${PROJECT_NAME}
)

# install libraries
ament_export_targets(export_${PROJECT_NAME} HAS_LIBRARY_TARGET)
ament_export_dependencies(rclcpp std_msgs)

install(
  DIRECTORY include/
  DESTINATION include/${PROJECT_NAME}
)
install(
  TARGETS custom_pub_lib
  EXPORT export_${PROJECT_NAME}
  LIBRARY DESTINATION lib
  ARCHIVE DESTINATION lib
  RUNTIME DESTINATION bin
)

# install launch
install(
  DIRECTORY launch
  DESTINATION share/${PROJECT_NAME}
)

# install config
install(
  DIRECTORY config
  DESTINATION share/${PROJECT_NAME}
)

if(BUILD_TESTING)
  find_package(ament_lint_auto REQUIRED)
  # the following line skips the linter which checks for copyrights
  # comment the line when a copyright and license is added to all source files
  set(ament_cmake_copyright_FOUND TRUE)
  # the following line skips cpplint (only works in a git repo)
  # comment the line when this package is in a git repo and when
  # a copyright and license is added to all source files
  set(ament_cmake_cpplint_FOUND TRUE)
  ament_lint_auto_find_test_dependencies()
endif()

ament_package()