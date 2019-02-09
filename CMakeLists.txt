########################################################################
# Project setup
########################################################################
cmake_minimum_required(VERSION 2.8.0)
project(rx_tools)

#local include directories first
include_directories(${PROJECT_SOURCE_DIR}/src/convenience)

#include local cmake modules
list(APPEND CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/cmake)

MACRO( FIND_PATH_REQUIRED arg1 arg2 )
    FIND_PATH( ${arg1} ${arg2} ${ARGN} )
    IF( "${${arg1}}" STREQUAL "${arg1}-NOTFOUND"  )
        MESSAGE( FATAL_ERROR "${arg2} not found" )
    ELSE()
        MESSAGE( STATUS "${arg2} found in ${${arg1}}" )
    ENDIF()
ENDMACRO( FIND_PATH_REQUIRED )

########################################################################
# Dependencies
########################################################################
find_package(SoapySDR "0.5" NO_MODULE)
if (NOT SoapySDR_FOUND)
    message(FATAL_ERROR "Soapy SDR development files not found...")
endif ()
include_directories(${SoapySDR_INCLUDE_DIRS})
list(APPEND RX_TOOLS_LIBS ${SoapySDR_LIBRARIES})

IF(WIN32)
    # prepare dependencies under windows
    add_subdirectory(${PROJECT_SOURCE_DIR}/src/fftw ${PROJECT_SOURCE_DIR}/src/fftw)
ELSE()
    MESSAGE( AUTHOR_WARNING "FFTw Must already be present and installed on this system" )
ENDIF(WIN32)

# Configure to link FFTw
FIND_PATH_REQUIRED( FFTW_INC fftw3.h PATHS "${PROJECT_SOURCE_DIR}/src/fftw/x64" "${PROJECT_SOURCE_DIR}/src/fftw/x86" PATH_SUFFIXES include )
INCLUDE_DIRECTORIES( ${FFTW_INC} )
IF(WIN32)
    MESSAGE( STATUS "Win32: using libfftw3-3.lib for linkage" )
    FIND_LIBRARY( FFTW_LIB libfftw3-3.lib REQUIRED PATHS ${FFTW_INC} ${FFTW_INC}/../lib )
    STRING( REPLACE ".lib" ".dll" FFTW_DLL ${FFTW_LIB} )
ELSEIF(UNIX)
    MESSAGE( STATUS "unix: using libfftw3.a for linkage" )
    FIND_LIBRARY( FFTW_LIB fftw3 REQUIRED PATHS ${FFTW_INC} ${FFTW_INC}/../lib )
    MESSAGE( STATUS "unix: ${FFTW_LIB}" )
ELSE()
    # https://cmake.org/Wiki/CMake_Useful_Variables
    MESSAGE( WARNING "Untested for ${CMAKE_SYSTEM_NAME}:${CMAKE_CXX_COMPILER_ID}" )
    FIND_LIBRARY( FFTW_LIB fftw3 REQUIRED PATHS ${FFTW_INC} ${FFTW_INC}/../lib )
    MESSAGE( STATUS "unix: ${FFTW_LIB}" )
ENDIF()
list(APPEND RX_TOOLS_LIBS ${FFTW_LIB})

#link with libm when available
find_library(
    MATH_LIBRARIES NAMES m
    PATHS /usr/lib /usr/lib64
)
if (MATH_LIBRARIES)
    list(APPEND RX_TOOLS_LIBS ${MATH_LIBRARIES})
endif ()

#link with pthreads
set(THREADS_USE_PTHREADS_WIN32 true)
find_package(Threads)
if (NOT THREADS_FOUND)
    message(FATAL_ERROR "pthreads development files not found...")
endif ()
include_directories(${THREADS_PTHREADS_INCLUDE_DIR})
list(APPEND RX_TOOLS_LIBS ${CMAKE_THREAD_LIBS_INIT})
message(STATUS "THREADS_PTHREADS_INCLUDE_DIR: ${THREADS_PTHREADS_INCLUDE_DIR}")
message(STATUS "CMAKE_THREAD_LIBS_INIT: ${CMAKE_THREAD_LIBS_INIT}")

#windows getopt compatibility
if (WIN32)
    include_directories(${PROJECT_SOURCE_DIR}/src/getopt)
    list(APPEND COMMON_SOURCES src/getopt/getopt.c)
endif ()

########################################################################
# Helper library
########################################################################
set(CMAKE_POSITION_INDEPENDENT_CODE TRUE)
list(APPEND COMMON_SOURCES src/convenience/convenience.c)
add_library(common STATIC ${COMMON_SOURCES})
list(APPEND RX_TOOLS_LIBS common)

########################################################################
# Build executables
########################################################################
add_executable(rx_fm src/rtl_fm.c)
target_link_libraries(rx_fm ${RX_TOOLS_LIBS})

add_executable(rx_power src/rtl_power.c)
target_link_libraries(rx_power ${RX_TOOLS_LIBS})

add_executable(rx_sdr src/rtl_sdr.c)
target_link_libraries(rx_sdr ${RX_TOOLS_LIBS})

add_executable(rx_rss src/rtl_rss.c)
target_link_libraries(rx_rss ${RX_TOOLS_LIBS})

# Handle linking and post-build steps
IF(WIN32)
    # http://stackoverflow.com/questions/10046114/in-cmake-how-can-i-test-if-the-compiler-is-clang
    # http://stackoverflow.com/questions/7988297/cmake-to-add-vs2010-project-custom-build-events
    IF(CMAKE_CXX_COMPILER_ID MATCHES "MSVC" OR MINGW)
        MESSAGE( STATUS "FFTW_DLL=${FFTW_DLL}" )
        add_custom_command(TARGET rx_rss
                           POST_BUILD
                           COMMAND ${CMAKE_COMMAND} -E copy "${FFTW_DLL}" \$\(OutDir\)
                           COMMENT "Copy ${FFTW_DLL} to OutDir")
    ENDIF()
ENDIF()

########################################################################
# Install executables
########################################################################
install(TARGETS rx_fm rx_power rx_sdr rx_rss DESTINATION bin)
