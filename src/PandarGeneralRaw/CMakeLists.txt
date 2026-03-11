cmake_minimum_required(VERSION 3.5)
project(PandarGeneral)

find_package(Boost REQUIRED COMPONENTS thread)
find_package(Eigen3 REQUIRED)
find_package(PCL REQUIRED COMPONENTS common)
find_package(Threads REQUIRED)

if(NOT MSVC)
  set(CMAKE_CXX_FLAGS "-fPIC --std=c++14")
else()
  set(CMAKE_CXX_STANDARD 14)
  add_definitions(-D_CRT_SECURE_NO_WARNINGS -D_WINSOCK_DEPRECATED_NO_WARNINGS -DWIN32_LEAN_AND_MEAN)
endif()

include_directories(
	.
	include
	include/pandarGeneral
	${Boost_INCLUDE_DIRS}
	${PCL_INCLUDE_DIRS}
)

add_library(${PROJECT_NAME} STATIC
	src/input.cc
	src/pandarGeneral_internal.cc
	src/pandarGeneral.cc
	src/pcap_reader.cpp
)

target_link_libraries(${PROJECT_NAME}
	Boost::thread
	Eigen3::Eigen
	Threads::Threads
)

find_package(libpcap REQUIRED)
target_link_libraries(${PROJECT_NAME} libpcap::libpcap)
if(WIN32)
  target_link_libraries(${PROJECT_NAME} ws2_32)
endif()

if(${CMAKE_SOURCE_DIR} STREQUAL ${CMAKE_CURRENT_SOURCE_DIR})

endif(${CMAKE_SOURCE_DIR} STREQUAL ${CMAKE_CURRENT_SOURCE_DIR})
