
cmake_minimum_required(VERSION 3.1)
project(nvmedirect)

set(CMAKE_C_STANDARD 11)
set(CMAKE_C_FLAGS "-Wall -fPIC")
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_FLAGS "-Wall -fPIC")

#Build the kernel module
add_custom_target(build-module COMMAND bash ${CMAKE_SOURCE_DIR}/modules/build-module.sh ${CMAKE_SOURCE_DIR})
add_custom_target(insert-module COMMAND sudo insmod ${CMAKE_SOURCE_DIR}/modules/nvmed.ko)
add_custom_target(remove-module COMMAND sudo insmod nvmed)
add_custom_target(print-klog COMMAND dmesg)
add_custom_target(clear-klog COMMAND sudo dmesg --clear)

#Build the NVMeDirect library
add_library(libnvmed SHARED library/lib_nvmed.c library/utils/radix-tree.c)
target_link_libraries(libnvmed -pthread -shared)

#TODO: Create a program the uses NVMeDirect
add_executable(nvmed_test src/nvmedirect-io.cpp)
target_include_directories(nvmed_test PUBLIC include)
target_link_libraries(nvmed_test libnvmed)
