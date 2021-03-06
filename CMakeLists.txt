cmake_minimum_required(VERSION 3.0)
SET(CMAKE_INCLUDE_CURRENT_DIR ON)

project(ImageGraphCutSegmentation)
enable_testing()

add_subdirectory(CMakeHelpers)

# Compile with c++11 functionality.
if(UNIX)
  SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -std=gnu++11") # gcc >= 4.7
endif(UNIX)

# ITK
FIND_PACKAGE(ITK REQUIRED ITKCommon ITKIOImageBase ITKIOPNG ITKIOMeta
                          ITKImageIntensity ITKImageFeature
                          ITKMathematicalMorphology
                          ITKBinaryMathematicalMorphology ITKDistanceMap
                          ITKTestKernel)
INCLUDE( ${ITK_USE_FILE} )

if( "${ITK_VERSION_MAJOR}" LESS 4 )
  MESSAGE(FATAL_ERROR "You must build ImageGraphCutSegmentation with ITK >= 4.0!")
endif( "${ITK_VERSION_MAJOR}" LESS 4 )

# Boost
set(Boost_USE_MULTITHREADED ON)
FIND_PACKAGE(Boost 1.50)
INCLUDE_DIRECTORIES(${Boost_INCLUDE_DIRS})

# Submodules
UseSubmodule(Mask ImageGraphCutSegmentation)

# Allow this project to be detected and used as a submodule
CreateSubmodule(ImageGraphCutSegmentation)

# Give the compiler all of the required include directories
include_directories(${ImageGraphCutSegmentation_include_dirs})

# Make the h/hpp files appear in a QtCreator project
add_custom_target(ImageGraphCut SOURCES
ImageGraphCut.h ImageGraphCut.hpp PixelDifference.h README.md)

option(ImageGraphCutSegmentation_BuildExamples "Build ImageGraphCutSegmentation examples?")
if(ImageGraphCutSegmentation_BuildExamples)
  add_subdirectory(Examples)
endif()

option(ImageGraphCutSegmentation_BuildTests "Build ImageGraphCutSegmentation tests?")
if(ImageGraphCutSegmentation_BuildTests)
  add_subdirectory(Tests)
endif()
