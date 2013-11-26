cmake_minimum_required(VERSION 2.8)
project(ptex)

#-------------------------------------------------------------------------------
# Obtain Ptexture API version from version.h file
if(EXISTS "${CMAKE_SOURCE_DIR}/version.h")

    # parse version string from src/version.h
    file(STRINGS "${CMAKE_SOURCE_DIR}/version.h" PTEXTURE_VERSION REGEX "^#define PTEXURE_VERSION .*$")
    string(REPLACE "#define PTEXURE_VERSION" "" PTEXTURE_VERSION ${PTEXTURE_VERSION})

    # remove whitespace
    set(blah "   foo bar  ")
    string(REGEX REPLACE "[ \t\r\n]+" "" PTEXTURE_VERSION ${PTEXTURE_VERSION})
    string(REGEX REPLACE "[ \t\r\n]+$" "" PTEXTURE_VERSION ${PTEXTURE_VERSION})
else()
    message(FATAL_ERROR "Cannot locate ${CMAKE_SOURCE_DIR}/version.h in CMAKE_SOURCE_DIR")
endif()

message(STATUS "Generating build for ${CMAKE_PROJECT_NAME} version \"${PTEXTURE_VERSION}\"")

#-------------------------------------------------------------------------------
# Detect Clang (until a cmake version provides built-in variables)
if("${CMAKE_CXX_COMPILER_ID}" MATCHES "Clang")
    set(CMAKE_COMPILER_IS_CLANGCC 1)
elseif("${CMAKE_CXX_COMPILER_ID}" MATCHES "Intel")
    set(CMAKE_COMPILER_IS_ICC 1)
endif()

# Intel's icc compiler requires some libraries linked
if(CMAKE_COMPILER_IS_ICC)

    foreach (ICC_LIB iomp5 irng intlc)

        if(CMAKE_SIZEOF_VOID_P MATCHES "8")
            list(APPEND ICC_LIB_ARCH "intel64")
        elseif(CMAKE_SIZEOF_VOID_P MATCHES "4")
            list(APPEND ICC_LIB_ARCH "ia32")
        endif()

        find_library( ICC_${ICC_LIB}
            NAMES
                ${ICC_LIB}
            PATHS
                ${ICC_LOCATION}/lib
                /opt/intel/lib/
            PATH_SUFFIXES
                ${ICC_LIB_ARCH}
        )

        if (ICC_${ICC_LIB})
            list(APPEND ICC_LIBRARIES ${ICC_${ICC_LIB}})
        else()
            message( FATAL_ERROR "${ICC_${ICC_LIB}} library not found - required by icc" )
        endif()

    endforeach()
endif()

set(PLATFORM_LIBRARIES ${ICC_LIBRARIES})

list(APPEND CMAKE_INSTALL_RPATH ${CMAKE_INSTALL_PREFIX}/lib)

# add the automatically determined parts of the RPATH
# which point to directories outside the build tree to the install RPATH
SET(CMAKE_INSTALL_RPATH_USE_LINK_PATH TRUE)

#-------------------------------------------------------------------------------
# enable testing for the project
include(CTest)
enable_testing()

include(FindZLIB)
include(FindThreads)

include_directories(ptex)
include_directories(${ZLIB_INCLUDE_DIR})

#-------------------------------------------------------------------------------
add_subdirectory(ptex)
add_subdirectory(utils)
add_subdirectory(tests)
add_subdirectory(doc)
