set(test_sources
    main1
    main3
    main4
    main5
    main6
    main7
    main8
    main9
    main10
    main11
    main12
    main13
    main14
    main15
    main16
    main17
    test-for
    test-gensym
    test-regressions
    test-threading
)

file(GENERATE OUTPUT build-test
     CONTENT "#!/bin/bash\nTOCCATA_DIR=${CMAKE_SOURCE_DIR} ${CMAKE_BINARY_DIR}/toccata $@\n")

set(CMAKE_C_FLAGS "-O3")

foreach(test ${test_sources})
    add_custom_command(OUTPUT ${CMAKE_CURRENT_BINARY_DIR}/${test}.c 
        COMMAND sh ${CMAKE_BINARY_DIR}/build-test ${CMAKE_CURRENT_SOURCE_DIR}/${test}.toc >${CMAKE_CURRENT_BINARY_DIR}/${test}.c
        DEPENDS ${test}.toc)

    include_directories(${CMAKE_SOURCE_DIR})

    add_executable(${test} ${CMAKE_CURRENT_BINARY_DIR}/${test}.c)
    target_link_libraries(${test} toccata-core Threads::Threads)
    set_property(TARGET ${test} PROPERTY C_STANDARD 99)
    
    add_test(NAME ${test} 
             COMMAND ${test})
endforeach(test)
