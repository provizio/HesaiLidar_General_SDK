cmake_minimum_required(VERSION 3.5)
project(PandarGeneralSDK)

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

add_subdirectory(src/PandarGeneralRaw)

include_directories(
    .
    include
    src/PandarGeneralRaw/include
    ${Boost_INCLUDE_DIRS}
    ${PCL_INCLUDE_DIRS}
)

add_library(${PROJECT_NAME} STATIC
    src/pandarGeneral_sdk.cc
    src/tcp_command_client.c
    src/util.c
    src/wrapper.cc
)

target_link_libraries(${PROJECT_NAME}
    PandarGeneral
    Boost::thread
    Eigen3::Eigen
    Threads::Threads
)

if(WIN32)
  find_package(libpcap REQUIRED)
  target_link_libraries(${PROJECT_NAME} libpcap::libpcap ws2_32)
else()
  target_link_libraries(${PROJECT_NAME} pcap)
endif()

if(${CMAKE_SOURCE_DIR} STREQUAL ${CMAKE_CURRENT_SOURCE_DIR})
    find_package(PCL REQUIRED)
    add_executable(PandarGeneralTest
        test/test.cc
    )

    target_link_libraries(PandarGeneralTest
        ${PROJECT_NAME}
        ${PCL_IO_LIBRARIES}
        Eigen3::Eigen
        Boost::thread
    )
endif(${CMAKE_SOURCE_DIR} STREQUAL ${CMAKE_CURRENT_SOURCE_DIR})
