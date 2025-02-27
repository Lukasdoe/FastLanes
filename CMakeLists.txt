cmake_minimum_required(VERSION 3.20)
message(STATUS "Building using CMake version: ${CMAKE_VERSION}")

set(CMAKE_CXX_STANDARD 17)
project(FastLanes)

# CMAKE_CXX_FLAGS now fully assembled
message(STATUS "CMAKE_C_FLAGS: ${CMAKE_C_FLAGS}")
message(STATUS "CMAKE_CXX_FLAGS: ${CMAKE_CXX_FLAGS}")

include_directories(${CMAKE_CURRENT_BINARY_DIR}/src)
include_directories(src)

#[[===================================================================================================================]]
#-----------------------------------------------------------------------------------------------------------------------
include(FetchContent)
include(CheckCXXCompilerFlag)
include(CMakePrintHelpers)
include(CTest)
# FMT: -----------------------------------------------------------------------------------------------------------------
FetchContent_Declare(fmt
                     GIT_REPOSITORY https://github.com/fmtlib/fmt.git
                     GIT_TAG master
                     )
FetchContent_MakeAvailable(fmt)
include_directories(${fmt_SOURCE_DIR}/include)
# Gtest: ---------------------------------------------------------------------------------------------------------------
include(GoogleTest)
FetchContent_Declare(googletest
                     URL https://github.com/google/googletest/archive/609281088cfefc76f9d0ce82e1ff6c30cc3591e5.zip)
# For Windows: Prevent overriding the parent project's compiler/linker settings
set(gtest_force_shared_crt ON CACHE BOOL "" FORCE)
FetchContent_MakeAvailable(googletest)
#-----------------------------------------------------------------------------------------------------------------------
include_directories(fls_generated/include)
#[[===================================================================================================================]]
# FLS Benchmark: -------------------------------------------------------------------------------------------------------
add_subdirectory(fls_generated)
#[[===================================================================================================================]]

set(CMAKE_INSTALL_PREFIX "${CMAKE_BINARY_DIR}/install")

install(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/fls_generated/include/fls_gen DESTINATION include)
install(
        TARGETS
        fallback_scalar_nav_1024_uf1_rsum
        fallback_unit64_nav_1024_uf1_rsum
        x86_64_sse_intrinsic_1024_uf1_rsum
        x86_64_avx2_intrinsic_1024_uf1_rsum
        x86_64_avx512bw_intrinsic_1024_uf1_rsum
        EXPORT rsumTargets
        LIBRARY DESTINATION lib
        ARCHIVE DESTINATION lib
        RUNTIME DESTINATION run
        INCLUDES DESTINATION include
)
install(EXPORT rsumTargets
        FILE rsumTargets.cmake
        NAMESPACE rsum::
        DESTINATION lib/cmake/rsum
)
