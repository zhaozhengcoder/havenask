cmake_minimum_required(VERSION 3.1)

PROJECT(havenask)

set(CMAKE_BUILD_TYPE "RELEASE")
set(CMAKE_CXX_FLAGS_DEBUG "$ENV{CXXFLAGS} -O0 -Wall -g -ggdb")
set(CMAKE_CXX_FLAGS_RELEASE "$ENV{CXXFLAGS} -O3 -Wall")
set(CMAKE_CXX_FLAGS "-std=c++14 ${CMAKE_CXX_FLAGS} -g -ftest-coverage -fprofile-arcs -Wno-deprecated")

# pb文件的自动编译（前提需要在本地安装好protobuf）
find_package(Protobuf REQUIRED)
FILE(GLOB_RECURSE protofiles "*.proto")
LIST(APPEND PROTO_FLAGS -I${CMAKE_SOURCE_DIR})

foreach(protofile ${protofiles})
    message("proto_file" = ${protofile})
    get_filename_component(proto_file_name ${protofile} NAME_WE)
    get_filename_component(proto_file_path ${protofile} DIRECTORY)
    execute_process(
            COMMAND ${PROTOBUF_PROTOC_EXECUTABLE} -I=${CMAKE_SOURCE_DIR} --cpp_out=/Users/zzhao/Documents/GitHub/havenask_deps/service_proto ${protofile}
    )
endforeach()

# add_executable的所有文件, 递归的发现并保存在SRC_LIST中
file(GLOB_RECURSE SRC_LIST "*.hpp" "*.cpp" "*.cc"  "*.h" "*.c++" "*.capnp" "../deps_yara/*.cc" "../deps_yara/*.cpp" "../deps_yara/*.h")

#add_compile_definitions(ENABLE_Yara=1)
#add_compile_definitions(ENABLE_ANN=1)

# 依赖库的路径include
include_directories(
        ./
        ./aios/
        ./aios/ha3
        ./aios/ha3/ha3
        ./aios/resource_reader
        ./aios/future_lite
        ./aios/autil
        ./aios/matchdoc
        ./aios/memory
        ./aios/filesystem
        ./aios/expression
        ./aios/alog
        ./aios/alog/include
        ./aios/alog/include/alog
        ./aios/common
        ./aios/suez
        ./aios/apps/facility/
        ./aios/apps/facility/build_service
        ./aios/storage
        ./aios/storage/indexlib
        ./aios/unittest_framework/unittest
        ./aios/unittest_framework

        ../havenask_deps/service_proto/
        ../havenask_deps/service_proto/aios
        ../havenask_deps/service_proto/aios/storage
)

add_executable(havenask ${SRC_LIST})

target_link_libraries(havenask PRIVATE protobuf::libprotobuf)