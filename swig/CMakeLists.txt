cmake_minimum_required(VERSION 3.18)
set(CMAKE_VERBOSE_MAKEFILE OFF)
set(CMAKE_BUILD_TYPE Release)
project(inverter_wrapper)
set(CXX_STANDARD 11)
find_package(Python3 3.8 EXACT COMPONENTS Interpreter Development NumPy)
include(${CMAKE_BINARY_DIR}/conanbuildinfo.cmake)
conan_basic_setup()

find_package(SWIG REQUIRED)
include(${SWIG_USE_FILE})

include_directories(
  "../src" ${Python3_INCLUDE_DIRS} ${Python3_NumPy_INCLUDE_DIRS}
  "${CONAN_INCLUDE_DIRS_EIGEN}/Eigen")
# file(GLOB sourceFiles "../src/*.c")

if(CMAKE_HOST_SYSTEM_NAME MATCHES "Windows")
  set(Linker_options ${Python3_LIBRARIES})
elseif(CMAKE_HOST_SYSTEM_NAME MATCHES "Darwin")
  set(Linker_options "-undefined dynamic_lookup")
endif()

set(CMAKE_SWIG_FLAGS "")
set_source_files_properties(inverter_wrapper.i PROPERTIES CPLUSPLUS ON)
# set_source_files_properties(inverter_wrapper.i PROPERTIES SWIG_FLAGS
# "-includeall") set_source_files_properties(inverter_wrapper.i PROPERTIES
swig_add_library(
  inverter_wrapper
  TYPE MODULE
  LANGUAGE python
  SOURCES inverter_wrapper.i ../src/inverter.cpp)
swig_link_libraries(inverter_wrapper ${Linker_options})

install(TARGETS inverter_wrapper DESTINATION "${PROJECT_SOURCE_DIR}/pyinverter")
install(FILES "${CMAKE_BINARY_DIR}/swig/inverter_wrapper.py"
        DESTINATION "${PROJECT_SOURCE_DIR}/pyinverter")
