project(FrostHello)
cmake_minimum_required(VERSION 2.8)
find_library(libfrost frost)
find_package(ICU COMPONENTS uc i18n REQUIRED)

function(compile_frost out_var)
    set(result)
    foreach(input ${ARGN})
        get_filename_component(outputDir ${input} DIRECTORY)
        get_filename_component(outputName ${input} NAME_WE)
        set(output "${CMAKE_CURRENT_BINARY_DIR}/out/${outputDir}/${outputName}.o")
        add_custom_command(
            OUTPUT ${output}
            COMMAND frostc -f o -o ${output} ${CMAKE_CURRENT_SOURCE_DIR}/src/${input}
            DEPENDS ${CMAKE_CURRENT_SOURCE_DIR}/src/${input}
            WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}
        )
        list(APPEND result ${output})
    endforeach()
    set(${out_var} "${result}" PARENT_SCOPE)
endfunction()

compile_frost(frost_objects
    Main.frost
)

add_executable(hello
    ${frost_objects}
)
set_target_properties(hello PROPERTIES LINKER_LANGUAGE C)
target_link_libraries(hello ${libfrost} ${ICU_LIBRARIES})
