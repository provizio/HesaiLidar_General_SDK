cmake_minimum_required(VERSION 2.8)
project(PandarGeneralSDK)

find_package( Boost REQUIRED )
find_package( Eigen3 REQUIRED )
find_package( PCL REQUIRED COMPONENTS common )
find_package( Threads REQUIRED )

set (CMAKE_CXX_FLAGS "-fPIC --std=c++14")

add_subdirectory(src/PandarGeneralRaw)

include_directories(
    .
    include
    src/PandarGeneralRaw/include
    ${Boost_INCLUDE_DIRS}
    ${PCL_INCLUDE_DIRS}
)

add_library( ${PROJECT_NAME} SHARED
    src/pandarGeneral_sdk.cc
    src/tcp_command_client.c
    src/util.c
)

target_link_libraries(${PROJECT_NAME} 
    PandarGeneral
    ${PCL_IO_LIBRARIES}
    ${Boost_LIBRARIES}
    boost_thread
    Eigen3::Eigen
    pcap
    Threads::Threads
)

if(${CMAKE_SOURCE_DIR} STREQUAL ${CMAKE_CURRENT_SOURCE_DIR})
    find_package(PCL REQUIRED)
    add_executable(PandarGeneralTest
        test/test.cc
    )

    target_link_libraries(PandarGeneralTest
        ${PROJECT_NAME}
        ${PCL_IO_LIBRARIES}
        ${Boost_LIBRARIES}
        boost_thread
        Eigen3::Eigen
        Threads::Threads
    )
endif(${CMAKE_SOURCE_DIR} STREQUAL ${CMAKE_CURRENT_SOURCE_DIR})

