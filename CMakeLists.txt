cmake_minimum_required( VERSION 2.8.9 )

project(clandmark)

# The version number
set(clandmark_VERSION_MAJOR 1)
set(clandmark_VERSION_MINOR 5)

set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_SOURCE_DIR}/cmake/Modules")

#option(BUILD_SHARED_LIBS "Check whether to build libraries dynamically or statically. On for dynamic, Off for static build." ON)
mark_as_advanced(CMAKE_INSTALL_PREFIX)
set(BUILD_SHARED_LIBS "TRUE" CACHE BOOL "Global flag to cause add_library to create shared libraries if on.")
set(CMAKE_SKIP_BUILD_RPATH "FALSE" CACHE BOOL "Do not include RPATHs in the build tree.")

if(NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE RELEASE CACHE STRING
	"Choose the type of build, options are: None Debug Release RelWithDebInfo MinSizeRel."
	FORCE
    )
endif(NOT CMAKE_BUILD_TYPE)

option(DOUBLE_PRECISION "Set the default precision used in CLandmark." ON)
option(BUILD_MATLAB_BINDINGS "Check whether to build MATLAB interface." OFF)
option(BUILD_PYTHON_BINDINGS "Check whether to build Python interface." OFF)
option(BUILD_CPP_EXAMPLES "Check whether to build CPP examples." ON)
option(USE_OPENMP "Enable/Disable OpenMP." OFF)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -pedantic")
set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} -Wall -pedantic")
set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} -Wall -pedantic")

# build clandmark
add_subdirectory (libclandmark)

## build flandmark
#add_subdirectory (libflandmark)

# build examples
if(BUILD_CPP_EXAMPLES)
    add_subdirectory(examples)
    # copy important files needed to run examples (opencv haarcascade for facial detector, flandmark_model file, example images and videos)
    configure_file(${CMAKE_CURRENT_SOURCE_DIR}/data/flandmark_model.xml ${CMAKE_CURRENT_BINARY_DIR}/examples/flandmark_model.xml COPYONLY)
    configure_file(${CMAKE_CURRENT_SOURCE_DIR}/data/face.jpg ${CMAKE_CURRENT_BINARY_DIR}/examples/face.jpg COPYONLY)
    configure_file(${CMAKE_CURRENT_SOURCE_DIR}/data/haarcascade_frontalface_alt.xml ${CMAKE_CURRENT_BINARY_DIR}/examples/haarcascade_frontalface_alt.xml COPYONLY)
endif(BUILD_CPP_EXAMPLES)

# build MATLAB mex-files
if(BUILD_MATLAB_BINDINGS)
    add_subdirectory(matlab_interface)
endif(BUILD_MATLAB_BINDINGS)

# build Python interface
if(BUILD_PYTHON_BINDINGS)
    add_subdirectory(python_interface)
endif(BUILD_PYTHON_BINDINGS)

# propagate precision definition
if(DOUBLE_PRECISION)
    add_definitions( -DDOUBLE_PRECISION=1 )
else(DOUBLE_PRECISION)
    add_definitions( -DDOUBLE_PRECISION=0 )
endif(DOUBLE_PRECISION)

