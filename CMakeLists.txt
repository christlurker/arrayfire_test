cmake_minimum_required(VERSION 3.6)
project(arrayfire_test)

IF(NOT CMAKE_BUILD_TYPE)
    # SET(CMAKE_BUILD_TYPE Debug)
    SET(CMAKE_BUILD_TYPE Release)
ENDIF()

MESSAGE("Build type: " ${CMAKE_BUILD_TYPE})
set(CMAKE_CXX_STANDARD 11)

set(SOURCE_FILES main.cpp)

FIND_PACKAGE(ArrayFire REQUIRED)
FIND_PACKAGE(OpenCV REQUIRED)
FIND_PACKAGE(CUDA QUIET)

INCLUDE_DIRECTORIES(${ArrayFire_INCLUDE_DIRS})
INCLUDE_DIRECTORIES(${OpenCV_INCLUDE_DIRS})

IF(WIN32)
    # Deprecated Errors are Warning 4996 on VS2013.
    # https://msdn.microsoft.com/en-us/library/ttcz0bys.aspx
    IF(MSVC)
        SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} /we4996")
        SET(CMAKE_C_FLAGS   "${CMAKE_C_FLAGS} /we4996")
    ENDIF(MSVC)
ELSE(WIN32)
    SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Werror=deprecated-declarations")
    SET(CMAKE_C_FLAGS   "${CMAKE_C_FLAGS} -Werror=deprecated-declarations")
ENDIF(WIN32)

OPTION(BUILD_CUDA "Build ArrayFire Examples for CUDA backend" ON)
cuda_add_executable(arrayfire_test ${SOURCE_FILES})

target_link_libraries(arrayfire_test
        ${ArrayFire_CUDA_LIBRARIES}
        ${CUDA_LIBRARIES}
        ${CUDA_nppi_LIBRARY}
        ${OpenCV_LIBS}

        ${CUDA_CUBLAS_LIBRARIES}
        ${CUDA_LIBRARIES}
        ${CUDA_cusolver_LIBRARY}
        ${CUDA_cusparse_LIBRARY}
        ${CUDA_CUFFT_LIBRARIES}
        ${CUDA_nvvm_LIBRARY}
        ${CUDA_CUDA_LIBRARY}
        )