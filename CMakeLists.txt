cmake_minimum_required(VERSION 3.10)
project(SeamAwareDecimater)

SET(CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/cmake)

## We need Eigen
find_package(Eigen REQUIRED)
include_directories( "${Eigen_INCLUDE_DIR}" )

## We need libigl
find_package(LIBIGL REQUIRED)
include_directories( "${LIBIGL_INCLUDE_DIR}" )

## We don't have/want MOSEK
add_definitions(-DIGL_NO_MOSEK)

link_directories(
	/usr/local/lib
) 

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# Set a default build type if none was specified
if(NOT CMAKE_BUILD_TYPE)
  message(STATUS "Setting build type to 'Release' as none was specified.")
  set(CMAKE_BUILD_TYPE Release CACHE STRING "Choose the type of build, options are: Debug, Release, RelWithDebInfo, MinSizeRel." FORCE)
endif()

if ("${CMAKE_CXX_COMPILER_ID}" STREQUAL "Clang")
  add_compile_options(-stdlib=libc++)
endif()

add_library(DEC_LIBS
    OBJECT
    quadric_error_metric.cpp
    cost_and_placement.cpp
    half_edge.cpp
    neighbor_faces_and_boundary.cpp
    detect_foldover.cpp
    decimate.cpp
    collapse_edge_seam.cpp
    )
    
add_executable(decimater
	decimater.cpp
	$<TARGET_OBJECTS:DEC_LIBS>
	)
target_link_libraries ( decimater
	${LIBIGL_LIBRARIES}
)
