cmake_minimum_required(VERSION 3.16)

project(ControllerImage)

if(TARGET SDL3::SDL3)
    set(SDL3_LIBRARIES SDL3::SDL3)
else()
    find_package(SDL3 CONFIG REQUIRED)
endif()

add_library(controllerimage STATIC
    src/controllerimage.c
)
target_include_directories(controllerimage PRIVATE
    src
    ${SDL3_INCLUDE_DIR}
)
set_target_properties(controllerimage PROPERTIES C_VISIBILITY_PRESET "hidden")
target_link_libraries(controllerimage PRIVATE ${SDL3_LIBRARIES})

add_executable(make-controllerimage-data src/make-controllerimage-data.c)

add_executable(test-controllerimage src/test-controllerimage.c)
target_link_libraries(test-controllerimage controllerimage ${SDL3_LIBRARIES})

add_executable(demo-controllerimage src/demo-controllerimage.c)
target_link_libraries(demo-controllerimage controllerimage ${SDL3_LIBRARIES})


add_custom_command(OUTPUT "${CMAKE_CURRENT_BINARY_DIR}/gamepad_front.bmp"
    COMMAND "${CMAKE_COMMAND}" -E copy "${CMAKE_CURRENT_SOURCE_DIR}/src/gamepad_front.bmp" "${CMAKE_CURRENT_BINARY_DIR}/gamepad_front.bmp"
    DEPENDS "${CMAKE_CURRENT_SOURCE_DIR}/src/gamepad_front.bmp"
)
add_custom_target(copy-test-resources
    DEPENDS "${CMAKE_CURRENT_BINARY_DIR}/gamepad_front.bmp"
)
add_dependencies(test-controllerimage copy-test-resources)


