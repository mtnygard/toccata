cmake_minimum_required(VERSION 3.9.0)

project(toccata VERSION 0.1.0)

add_definitions(-std=c99 -fno-objc-arc)

set(THREADS_PREFER_PTHREAD_FLAG ON)
find_package(Threads REQUIRED)

configure_file(config.h.in config.h)

add_executable(toccata toccata.c core.c config.h)
target_sources(toccata INTERFACE core.h)
target_link_libraries(toccata Threads::Threads ${llvm_libs})

add_library(toccata-core STATIC core.c core.h config.h)

add_subdirectory(tools)
add_subdirectory(examples)

enable_testing()
add_subdirectory(regression-tests)

add_custom_target(core SOURCES core.toc)

install(TARGETS toccata-core toccata
        RUNTIME DESTINATION bin 
        ARCHIVE DESTINATION lib)

install(FILES core.h ${CMAKE_CURRENT_BINARY_DIR}/config.h
        DESTINATION ${CMAKE_INSTALL_PREFIX}/include/toccata)

install(FILES core.toc 
        DESTINATION ${CMAKE_INSTALL_PREFIX}/lib/toccata)
