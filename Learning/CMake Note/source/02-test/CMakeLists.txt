cmake_minimum_required(VERSION 3.0)

project(hello_world)

message(STATUS "This is BINARY dir " ${PROJECT_BINARY_DIR})
message(STATUS "This is SOURCE dir " ${PROJECT_SOURCE_DIR})

add_executable(hello_world main.cc)
