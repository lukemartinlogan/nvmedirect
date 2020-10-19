
cmake_minimum_required(VERSION 3.1)
project(nvmedirect)

set(CMAKE_C_STANDARD 11)
set(CMAKE_C_FLAGS "-Wall -fPIC")
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_FLAGS "-Wall -fPIC")

#Build the kernel module
add_custom_target(build-module COMMAND bash ${CMAKE_SOURCE_DIR}/modules/build-module.sh ${CMAKE_SOURCE_DIR})
add_custom_target(insert-module COMMAND sudo insmod ${CMAKE_SOURCE_DIR}/modules/nvmed.ko)
add_custom_target(remove-module COMMAND sudo rmmod nvmed)
add_custom_target(print-klog COMMAND sudo dmesg)
add_custom_target(clear-klog COMMAND sudo dmesg --clear)

#Build the NVMeDirect library
add_library(libnvmed SHARED library/lib_nvmed.c library/utils/radix-tree.c)
target_link_libraries(libnvmed -pthread -shared)

#The example they made
add_executable(nvmed_ex example/nvmed_example/nvmed_example.c)
add_dependencies(nvmed_ex libnvmed)
target_link_libraries(nvmed_ex -pthread libnvmed)

#TODO: Create a program the uses NVMeDirect
add_executable(nvmed_test src/nvmedirect-io.cpp)
target_include_directories(nvmed_test PUBLIC include)
target_link_libraries(nvmed_test libnvmed)

# Enable tests
option(BUILD_TEST "Build the unit tests" ON)
if(BUILD_TEST)
    enable_testing()
    include(CTest)
endif()

add_test(NAME nvmed_ex_test COMMAND ${CMAKE_BINARY_DIR}/nvmed_ex /dev/nvme0n1)
set_property(TEST nvmed_ex_test APPEND PROPERTY ENVIRONMENT "LD_LIBRARY_PATH=${CMAKE_BINARY_DIR}")