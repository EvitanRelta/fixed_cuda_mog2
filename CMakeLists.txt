cmake_minimum_required(VERSION 3.16)

project(fixed_cuda_mog2 LANGUAGES CXX CUDA)

set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CUDA_STANDARD 14)
set(CMAKE_CUDA_STANDARD_REQUIRED ON)

# Always export a compilation database (ie. compile_commands.json) for Clangd
set(CMAKE_EXPORT_COMPILE_COMMANDS
    ON
    CACHE BOOL "Generate compile_commands.json file" FORCE)

find_package(CUDA REQUIRED)
find_package(OpenCV REQUIRED COMPONENTS core video cudaarithm)

add_library(fixed_cuda_mog2 STATIC
    src/mog2.cpp
    src/cuda/mog2.cu
)

target_compile_definitions(fixed_cuda_mog2 PRIVATE HAVE_CUDA)

target_include_directories(fixed_cuda_mog2
    PUBLIC
        include
    PRIVATE
        src
        ${CUDA_INCLUDE_DIRS}
)

target_link_libraries(fixed_cuda_mog2
    PUBLIC
        ${OpenCV_LIBS}
        ${CUDA_LIBRARIES}
)

set_target_properties(fixed_cuda_mog2 PROPERTIES
    CUDA_SEPARABLE_COMPILATION ON
    CUDA_RESOLVE_DEVICE_SYMBOLS ON
)

add_library(fixed_cuda_mog2::fixed_cuda_mog2 ALIAS fixed_cuda_mog2)
