cmake_minimum_required(VERSION 3.10)

# Set the minimum version of toolchain
set(CMAKE_C_STANDARD 99)
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# Set the name of project
project(lvgl_package)

# Add the subdirectory
add_subdirectory(lvgl)

include(${CMAKE_CURRENT_LIST_DIR}/lvgl/tests/FindLibDRM.cmake)

# Set the include directories for lvgl subdirectory
target_include_directories(lvgl 
    PUBLIC 
        ${PROJECT_SOURCE_DIR}
        ${Libdrm_INCLUDE_DIRS}
)
