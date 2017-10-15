cmake_minimum_required(VERSION 3.9.4)

project(toccata)

add_definitions(-std=c99)

find_package(LLVM REQUIRED)
find_package(LLVM REQUIRED CONFIG)

message(STATUS "Found LLVM ${LLVM_PACKAGE_VERSION}")
message(STATUS "Using LLVMConfig.cmake in: ${LLVM_DIR}")

include_directories(${LLVM_INCLUDE_DIRS})
add_definitions(${LLVM_DEFINITIONS})

llvm_map_components_to_libnames(llvm_libs support core irreader)

set (CORE_LIBS pthread)

add_executable(toccata toccata.c core.c)
target_link_libraries(toccata ${CORE_LIBS} ${llvm_libs})