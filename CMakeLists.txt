
cmake_minimum_required(VERSION 3.1)
project(nvmedirect)

set(CMAKE_C_STANDARD 11)
set(CMAKE_C_FLAGS "-Wall -fPIC")

add_custom_target(build-module COMMAND bash ${CMAKE_SOURCE_DIR}/modules/build-module.sh ${CMAKE_SOURCE_DIR})
add_custom_target(insert-module COMMAND sudo insmod ${CMAKE_SOURCE_DIR}/modules/nvmed.ko)
add_custom_target(remove-module COMMAND sudo insmod nvmed)
add_custom_target(print-klog COMMAND dmesg)
add_custom_target(clear-klog COMMAND sudo dmesg --clear)

add_library(libnvmed SHARED library/lib_nvmed.c library/utils/radix-tree.c)
target_link_libraries(libnvmed -pthread -shared)

#install(../include/nvme_hdr.h ../include/nvmed.h ../include/lib_nvmed.h ../include/radix-tree.h)
