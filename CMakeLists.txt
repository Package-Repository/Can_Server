cmake_minimum_required(VERSION 3.5)
project(unified_can_driver)

# Default to C99
if(NOT CMAKE_C_STANDARD)
  set(CMAKE_C_STANDARD 99)
endif()

if(NOT CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 20)
endif()

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

# find dependencies
find_package(ament_cmake REQUIRED)
find_package(rclcpp REQUIRED)
find_package(scion_types REQUIRED)

include_directories(include)
include_directories(include/module)

# target executable
add_library(
	${PROJECT_NAME}
	src/base_driver/unified_can_driver.cpp
	src/base_driver/can_send_service.cpp
	src/base_driver/can_mailbox_topic.cpp
	src/base_driver/send_command_service.cpp
	src/base_driver/state_decoder.cpp
	src/mbox_can.cpp
	src/module_loader.cpp
# Modules:
	src/module/dvl.cpp
	src/module/ms5837.cpp
	src/module/brlight.cpp
	src/module/brping1.cpp
	src/module/module.cpp
)

add_executable(
	${PROJECT_NAME}_exec
	main/main.cpp
)

target_link_libraries(
  ${PROJECT_NAME}_exec
  ${PROJECT_NAME}
  ${rclcpp_LIBRARIES}
)

ament_target_dependencies(${PROJECT_NAME} rclcpp scion_types)

install(TARGETS
	${PROJECT_NAME}_exec
	DESTINATION bin/robot/c)

install(TARGETS ${PROJECT_NAME}
  DESTINATION lib
)

set(INCLUDE_HEADERS
	include/can_commands.hpp
)

install(FILES ${INCLUDE_HEADERS} DESTINATION include/${PROJECT_NAME}) # Install Public Headers to Machine

# debug
add_compile_options(-g)

ament_package()
