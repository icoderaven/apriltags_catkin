# AprilTags2 CMake files
# Humphrey Hu, 2014
# Originally by Michael Kaess

project(AprilTags)

cmake_minimum_required(VERSION 2.8)

# Defaults to release build
if (NOT DEFINED CMAKE_BUILD_TYPE OR CMAKE_BUILD_TYPE STREQUAL "")
  message(STATUS "No build type selected, default to Release")
  set(CMAKE_BUILD_TYPE "Release" CACHE STRING "" FORCE)
endif ()
message(STATUS "Build configuration: ${CMAKE_BUILD_TYPE}")

# Set install paths
set (LIBRARY_OUTPUT_PATH "${PROJECT_SOURCE_DIR}/lib" CACHE PATH
  "Target directory for all libraries.")
set (EXECUTABLE_OUTPUT_PATH "${PROJECT_SOURCE_DIR}/bin" CACHE PATH
  "Target directory for all executables.")

# Can only use C++0x in Ubuntu 10.04 GCC 4.6
add_definitions(-Wall -std=c++0x)

set(AprilTags_VERSION_MAJOR 1)
set(AprilTags_VERSION_MINOR 0)
set(AprilTags_VERSION "${AprilTags_VERSION_MAJOR}.${AprilTags_VERSION_MINOR}")

set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_SOURCE_DIR}/cmake")

# OpenCV for camera interface
find_package(OpenCV 2 REQUIRED HINTS /home/humphreh/Software/opencv-2.4.6.1/install)
message(STATUS "OpenCV found at: ${OpenCV_INCLUDE_DIRS}")
include_directories(${OpenCV_INCLUDE_DIRS})
list(APPEND AprilTags_LIBS ${OpenCV_LIBS})

find_package(Eigen REQUIRED)
include_directories(${EIGEN_INCLUDE_DIRS})
list(APPEND AprilTags_LIBS ${EIGEN_LIBRARIES})

# Camera drivers
find_package(V4L2 REQUIRED)
include_directories(${V4L2_INCLUDE_DIRS})
list(APPEND AprilTags_LIBS ${V4L2_LIBRARIES})

# AprilTag headers
include_directories("${PROJECT_SOURCE_DIR}/include")

set(INCLUDE_INSTALL_DIR "${CMAKE_INSTALL_PREFIX}/include/apriltags")
set(LIBRARY_INSTALL_DIR "${CMAKE_INSTALL_PREFIX}/lib")
set(BINARY_INSTALL_DIR "${CMAKE_INSTALL_PREFIX}/bin")

add_subdirectory(src)
add_subdirectory(example)

export(TARGETS apriltags
    FILE "${PROJECT_BINARY_DIR}/ArgusTargets.cmake")

configure_file(AprilTagsConfig.cmake.in
    "${PROJECT_BINARY_DIR}/AprilTagsConfig.cmake" @ONLY)

configure_file(AprilTagsConfigVersion.cmake.in
    "${PROJECT_BINARY_DIR}/AprilTagsConfigVersion.cmake" @ONLY)

install(FILES
    "${PROJECT_BINARY_DIR}/AprilTagsConfig.cmake"
    "${PROJECT_BINARY_DIR}/AprilTagsConfigVersion.cmake"
    DESTINATION "${CMAKE_INSTALL_PREFIX}" COMPONENT dev)

install(EXPORT AprilTagsTargets
    DESTINATION "${CMAKE_INSTALL_PREFIX}" COMPONENT dev)