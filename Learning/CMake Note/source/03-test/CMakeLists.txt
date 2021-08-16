cmake_minimum_required(VERSION 3.5 FATAL_ERROR)

project(test3 LANGUAGES CXX)

message("c++ compiler flages: ${CMAKE_CXX_FLAGS}")

add_executable(test3 main.cc)
target_compile_options(test3
	PRIVATE
	"-fPIC")
#target_link_libraries(test3 )
