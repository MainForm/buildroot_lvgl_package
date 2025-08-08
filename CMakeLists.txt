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

# Prevent the situation where LV_USE_LINUX_DRM is not defined.
if(NOT DEFINED LV_USE_LINUX_DRM)
    set(LV_USE_LINUX_DRM 0)
endif()

if(LV_USE_LINUX_DRM EQUAL 1)
    target_compile_definitions(lvgl PRIVATE LV_USE_LINUX_DRM=1)
    include(${CMAKE_CURRENT_LIST_DIR}/lvgl/tests/FindLibDRM.cmake)

    # Set the include directories for lvgl subdirectory
    target_include_directories(lvgl 
        PRIVATE 
            ${Libdrm_INCLUDE_DIRS}
    )
else()
    target_compile_definitions(lvgl PRIVATE LV_USE_LINUX_DRM=0)
endif()


# Prevent the situation where LV_USE_LINUX_DRM is not defined.
if(NOT DEFINED LV_USE_LIBINPUT)
    set(LV_USE_LIBINPUT 0)
endif()

if(LV_USE_LIBINPUT EQUAL 1)
    target_compile_definitions(lvgl PRIVATE LV_USE_LIBINPUT=1)

    # Find libinput using pkg-config
    pkg_check_modules(LIBINPUT REQUIRED libinput)

    # Set the include directories for lvgl subdirectory
    target_include_directories(lvgl 
        PRIVATE 
            ${LIBINPUT_INCLUDE_DIRS}
    )

    target_link_libraries(lvgl
        PRIVATE 
            ${LIBINPUT_LIBRARIES}
    )
else()
    target_compile_definitions(lvgl PRIVATE LV_USE_LIBINPUT=0)
endif()
