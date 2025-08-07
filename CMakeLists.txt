cmake_minimum_required(VERSION 3.10)

# Set the minimum version of toolchain
set(CMAKE_C_STANDARD 99)
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# Set the name of project
project(lvgl_package)

# Add the subdirectory
add_subdirectory(lvgl)

target_include_directories(lvgl 
    PUBLIC 
        ${PROJECT_SOURCE_DIR}
)

# Prevent the situation where LV_USE_LINUX_DRM is not defined.
if(NOT DEFINED LV_USE_LINUX_DRM)
    set(LV_USE_LINUX_DRM 0)
endif()

if(LV_USE_LINUX_DRM EQUAL 1)
    include(${CMAKE_CURRENT_LIST_DIR}/lvgl/tests/FindLibDRM.cmake)

    # Set the include directories for lvgl subdirectory
    target_include_directories(lvgl 
        PUBLIC 
            ${Libdrm_INCLUDE_DIRS}
    )
endif()
