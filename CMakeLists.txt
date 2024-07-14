cmake_minimum_required(VERSION 3.18)
project(uastar LANGUAGES CXX CUDA)

set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQUIRED True)

# Set the CUDA host compiler to gcc
set(CUDA_HOST_COMPILER "C:/Program Files/Microsoft Visual Studio/2022/Community/VC/Tools/MSVC/14.39.33519/bin/Hostx64/x64/cl.exe")

# Find CUDA
find_package(CUDAToolkit REQUIRED)

# Set CUDA NVCC flags
set(CUDA_NVCC_FLAGS_DEBUG "${CUDA_NVCC_FLAGS_DEBUG} -lineinfo -gencode arch=compute_60,code=sm_60 --ptxas-options=-v -Wno-deprecated-declarations")
set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} -rdynamic -Wall -DDEBUG -Wno-type-limits")
set(CUDA_NVCC_FLAGS_RELEASE "${CUDA_NVCC_FLAGS_RELEASE} -O2 -gencode arch=compute_60,code=sm_60 --ptxas-options=-v -Wno-deprecated-declarations")
set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} -O2 -Wno-type-limits")

# Include directories for libraries
include_directories(libs/moderngpu/include)
include_directories(libs/bitmap)
include_directories(src)

# Modern GPU source files
set(moderngpu_source
    libs/moderngpu/src/mgpucontext.cu
    libs/moderngpu/src/mgpuutil.cpp
)

# Add executable for uastar
add_executable(
    uastar
    src/main.cpp
    src/pathway/pathway.cpp
    src/pathway/CPU-solver.cpp
    src/pathway/GPU-solver.cu
    src/pathway/input/custom.cpp
    src/pathway/input/zigzag.cpp
    src/puzzle/puzzle.cu
    src/puzzle/database.cpp
    ${moderngpu_source}
)

# Add executable for demo-sort
add_executable(
    demo-sort
    utils/modern-sort.cu
    ${moderngpu_source}
)

# Compile features and link libraries
target_compile_features(uastar PUBLIC cxx_std_14)
target_link_libraries(uastar ${CUDAToolkit_LIBRARIES} ${CUDAToolkit_CUDA_LIBRARIES})
target_compile_features(demo-sort PUBLIC cxx_std_14)
target_link_libraries(demo-sort ${CUDAToolkit_LIBRARIES} ${CUDAToolkit_CUDA_LIBRARIES})
