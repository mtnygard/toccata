set(tool-sources
    toc-ir)

find_package(LLVM REQUIRED)
find_package(LLVM REQUIRED CONFIG)

message(STATUS "Found LLVM ${LLVM_PACKAGE_VERSION}")
message(STATUS "Using LLVMConfig.cmake in: ${LLVM_DIR}")

include_directories(${LLVM_INCLUDE_DIRS})
add_definitions(${LLVM_DEFINITIONS})

set(CMAKE_C_FLAGS "-O3")

llvm_map_components_to_libnames(llvm_libs support core mcjit interpreter AllTargetsInfos X86 ARM AArch64)
    
include_directories(${CMAKE_SOURCE_DIR})   

file(GENERATE OUTPUT build-toc
     CONTENT "#!/bin/bash\nTOCCATA_DIR=${CMAKE_SOURCE_DIR} ${CMAKE_BINARY_DIR}/toccata $@\n")
    
foreach(tool ${tool-sources})
    add_custom_command(OUTPUT ${CMAKE_CURRENT_BINARY_DIR}/${tool}.c 
        COMMAND sh ${CMAKE_BINARY_DIR}/build-toc  ${CMAKE_CURRENT_SOURCE_DIR}/${tool}.toc >${CMAKE_CURRENT_BINARY_DIR}/${tool}.c
        DEPENDS ${tool}.toc)

    add_executable(${tool} ${tool}.c)
    target_link_libraries(${tool} toccata-core Threads::Threads ${llvm_libs} c++)

    set_property(TARGET ${tool} PROPERTY C_STANDARD 99)
endforeach(tool)


