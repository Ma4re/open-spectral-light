cmake_minimum_required(VERSION 3.25)

project(OpenSpectralLight VERSION 0.1.0 LANGUAGES C CXX)

set(CMAKE_C_STANDARD 11)
set(CMAKE_C_STANDARD_REQUIRED ON)
set(CMAKE_C_EXTENSIONS OFF)
set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

option(OSL_BUILD_TESTS "Build OpenSpectralLight host tests" ON)

if(OSL_BUILD_TESTS)
  include(CTest)
  enable_testing()
endif()

# Production targets are added with the first implemented vertical slice.
# Target-specific warnings and embedded restrictions belong on those targets,
# not as global compiler flags that would leak into host tools or third-party code.
