set(tool-sources
    toc-ir)

find_package(LLVM REQUIRED)
find_package(LLVM REQUIRED CONFIG)

message(STATUS "Found LLVM ${LLVM_PACKAGE_VERSION}")
message(STATUS "Using LLVMConfig.cmake in: ${LLVM_DIR}")

include_directories(${LLVM_INCLUDE_DIRS})
add_definitions(${LLVM_DEFINITIONS})

llvm_map_components_to_libnames(llvm_libs support core)
    
include_directories(${CMAKE_SOURCE_DIR})   
    
foreach(tool ${tool-sources})
    add_custom_command(OUTPUT ${CMAKE_CURRENT_BINARY_DIR}/${tool}.c
                    COMMAND toccata ${CMAKE_CURRENT_SOURCE_DIR}/${tool}.toc >${CMAKE_CURRENT_BINARY_DIR}/${tool}.c
                    DEPENDS ${tool}.toc)

    add_definitions(-std=c99 -fno-objc-arc)
    
    add_executable(${tool} ${tool}.c)
    target_link_libraries(${tool} toccata-core ${CORE_LIBS} ${llvm_libs})
endforeach(tool)


