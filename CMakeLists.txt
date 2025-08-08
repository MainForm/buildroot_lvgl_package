cmake_minimum_required(VERSION 3.13)

# Set the minimum version of toolchain
set(CMAKE_C_STANDARD 99)
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

cmake_policy(SET CMP0079 NEW)

# Set the name of project
project(lvgl_package)

# Add the subdirectory
add_subdirectory(lvgl)

target_include_directories(lvgl 
    PUBLIC 
        ${PROJECT_SOURCE_DIR}
)

find_package(PkgConfig REQUIRED)

if(LVGL_GRAPHIC_DRM)
    # Set the LV_USE_LINUX_DRM in lv_conf.h.in to 1
    set(ENABLE_DRM 1)
    include(${CMAKE_CURRENT_LIST_DIR}/lvgl/tests/FindLibDRM.cmake)

    # Set the include directories for lvgl subdirectory
    target_include_directories(lvgl 
        PUBLIC 
            ${Libdrm_INCLUDE_DIRS}
    )
else()
    set(ENABLE_DRM 0)
endif()

if(LVGL_INPUT_LIBINPUT)
    set(ENABLE_LIBINPUT 1)
    target_compile_definitions(lvgl PUBLIC LV_USE_LIBINPUT=1)

    # Find libinput using pkg-config
    pkg_check_modules(LIBINPUT REQUIRED libinput)

    # Set the include directories for lvgl subdirectory
    target_include_directories(lvgl 
        PUBLIC 
            ${LIBINPUT_INCLUDE_DIRS}
    )

    target_link_libraries(lvgl
        PUBLIC 
            ${LIBINPUT_LIBRARIES}
    )
else()
    set(ENABLE_LIBINPUT 0)
endif()

configure_file(
    ${CMAKE_SOURCE_DIR}/lv_conf.h.in
    ${CMAKE_BINARY_DIR}/lv_conf.h
    @ONLY
)