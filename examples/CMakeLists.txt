set(example_sources
    apply-maybe
    apply-vector
    flat-map-list
    flat-map-maybe
    map-maybe
    map-vector)

file(GENERATE OUTPUT use-new-compiler
     CONTENT "#!/bin/bash\nTOCCATA_DIR=${CMAKE_SOURCE_DIR} ${CMAKE_BINARY_DIR}/toccata $@\n")

foreach(example ${example_sources})
    add_custom_command(OUTPUT ${CMAKE_CURRENT_BINARY_DIR}/${example}.c 
        COMMAND sh ${CMAKE_BINARY_DIR}/use-new-compiler ${CMAKE_CURRENT_SOURCE_DIR}/${example}.toc >${CMAKE_CURRENT_BINARY_DIR}/${example}.c
        DEPENDS ${example}.toc)

    add_definitions(-std=c99 -fno-objc-arc)

    include_directories(${CMAKE_SOURCE_DIR})

    add_executable(${example} ${CMAKE_CURRENT_BINARY_DIR}/${example}.c)
    target_link_libraries(${example} toccata-core ${CORE_LIBS})
endforeach(example)
