cmake_minimum_required(VERSION 3.9.4)

project(toccata VERSION 0.1.0)

add_definitions(-std=c99 -fno-objc-arc)

find_package(LLVM REQUIRED)
find_package(LLVM REQUIRED CONFIG)

message(STATUS "Found LLVM ${LLVM_PACKAGE_VERSION}")
message(STATUS "Using LLVMConfig.cmake in: ${LLVM_DIR}")

include_directories(${LLVM_INCLUDE_DIRS})
add_definitions(${LLVM_DEFINITIONS})

llvm_map_components_to_libnames(llvm_libs support core irreader)

configure_file(config.h.in config.h)

set(CORE_LIBS pthread)

add_executable(toccata toccata.c core.c config.h)
target_link_libraries(toccata ${CORE_LIBS} ${llvm_libs})

add_library(toccata-core STATIC core.c core.h config.h)
#set_target_properties(toccata-core PROPERTIES PUBLIC_HEADER core.h)
#set_target_properties(toccata-core PROPERTIES PUBLIC_HEADER ${CMAKE_CURRENT_BINARY_DIR}/config.h)

add_subdirectory(examples)

add_custom_target(core SOURCES core.toc)

install(TARGETS toccata-core toccata
        RUNTIME DESTINATION bin 
        ARCHIVE DESTINATION lib 
        PUBLIC_HEADER DESTINATION include/toccata
        )

install(FILES core.h ${CMAKE_CURRENT_BINARY_DIR}/config.h
        DESTINATION ${CMAKE_INSTALL_PREFIX}/include/toccata)

install(FILES core.toc 
        DESTINATION ${CMAKE_INSTALL_PREFIX}/lib/toccata)
