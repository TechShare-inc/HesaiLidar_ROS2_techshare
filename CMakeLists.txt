cmake_minimum_required(VERSION 3.5)
project(hesai_lidar)

# Default to C++14
if(NOT CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 14)
endif()
if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

find_package(Boost REQUIRED COMPONENTS thread system)
find_package(pcl_conversions REQUIRED)
find_package(ament_cmake REQUIRED)
find_package(ament_index_cpp REQUIRED)
find_package(rclcpp REQUIRED)
find_package(rclcpp_action REQUIRED)
find_package(rclcpp_components REQUIRED)
find_package(rosidl_typesupport_c REQUIRED)
find_package(rosidl_default_generators REQUIRED)
find_package(builtin_interfaces REQUIRED)
find_package(std_msgs REQUIRED)
find_package(sensor_msgs REQUIRED)
find_package(image_transport REQUIRED)
find_package(tf2 REQUIRED)
find_package(tf2_ros REQUIRED)
find_package(tf2_msgs REQUIRED)
find_package(tf2_geometry_msgs REQUIRED)
find_package(PCL REQUIRED COMPONENTS io)

ament_export_dependencies(rosidl_default_runtime)

rosidl_generate_interfaces(${PROJECT_NAME}
  "msg/PandarPacket.msg"
  "msg/PandarScan.msg"
  DEPENDENCIES builtin_interfaces 
)

# ライブラリのパスを追加
link_directories(${CMAKE_CURRENT_SOURCE_DIR}/lib)

# hesai_lidar_node 実行ファイル
add_executable(hesai_lidar_node
    src/main_ros2.cc
)

ament_target_dependencies(hesai_lidar_node
  "geometry_msgs"
  "ament_index_cpp"
  "pcl_conversions"
  "rcl_interfaces"
  "rclcpp"
  "rcutils"
  "sensor_msgs"
  "std_msgs"
  "tf2"
  "tf2_ros"
  "tf2_msgs"
  "tf2_geometry_msgs"
  "image_transport"
  "rclcpp_components"
)

target_include_directories(hesai_lidar_node PRIVATE
    src/HesaiLidar_General_SDK/include
    src/HesaiLidar_General_SDK/src/PandarGeneralRaw/include
    ${PCL_INCLUDE_DIRS}
    ${Boost_INCLUDE_DIRS}
)

target_link_libraries(hesai_lidar_node 
    ${PCL_IO_LIBRARIES}
    ${Boost_SYSTEM_LIBRARY}
    PandarGeneral
    PandarGeneralSDK
)

rosidl_target_interfaces(hesai_lidar_node ${PROJECT_NAME} "rosidl_typesupport_cpp")

# ライブラリと実行ファイルのインストール設定
install(TARGETS hesai_lidar_node
  DESTINATION lib/${PROJECT_NAME})

install(DIRECTORY launch
  DESTINATION share/${PROJECT_NAME})

ament_package()
