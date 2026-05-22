cmake_minimum_required(VERSION 3.18)

project(mog2_cuda_fix LANGUAGES CXX CUDA)

set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CUDA_STANDARD 14)
set(CMAKE_CUDA_STANDARD_REQUIRED ON)

# Always export a compilation database (ie. compile_commands.json) for Clangd
set(CMAKE_EXPORT_COMPILE_COMMANDS
    ON
    CACHE BOOL "Generate compile_commands.json file" FORCE)

find_package(CUDAToolkit REQUIRED)
find_package(OpenCV REQUIRED COMPONENTS core video cudaarithm)

add_library(mog2_cuda STATIC
    src/mog2.cpp
    src/cuda/mog2.cu
)

target_compile_definitions(mog2_cuda PRIVATE HAVE_CUDA)

target_include_directories(mog2_cuda
    PUBLIC
        src/include
    PRIVATE
        src
)

target_link_libraries(mog2_cuda
    PUBLIC
        ${OpenCV_LIBS}
        CUDA::cudart
)

set_target_properties(mog2_cuda PROPERTIES
    CUDA_SEPARABLE_COMPILATION ON
)
