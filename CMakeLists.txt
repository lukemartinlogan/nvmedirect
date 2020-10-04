
cmake_minimum_required(VERSION 3.1)
project(nvmedirect)

set(CMAKE_C_STANDARD 11)
set(CMAKE_C_FLAGS "-Wall -fPIC")

add_custom_target(build-modules COMMAND make -C modules)

add_library(libnvmed library/lib_nvmed.c library/utils/radix-tree.c)
target_include_directories(libnvmed PUBLIC /lib/modules/4.3.3-040303-generic/build/include)
target_link_libraries(libnvmed -pthread -shared)

#install(../include/nvme_hdr.h ../include/nvmed.h ../include/lib_nvmed.h ../include/radix-tree.h)
