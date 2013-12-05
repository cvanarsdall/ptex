if (WIN32)
  add_definitions ( /DPTEX_EXPORTS )
endif (WIN32)

set(SRCS
    PtexCache.cpp
    PtexFilters.cpp
    PtexHalf.cpp
    PtexReader.cpp
    PtexSeparableFilter.cpp
    PtexSeparableKernel.cpp
    PtexTriangleFilter.cpp
    PtexTriangleKernel.cpp
    PtexUtils.cpp
    PtexWriter.cpp)

set(PUBLIC_HEADER_FILES
    PtexCache.h
    PtexDict.h
    PtexHalf.h
    PtexHashMap.h
    PtexInt.h
    PtexIO.h
    PtexMutex.h
    PtexPlatform.h
    PtexReader.h
    PtexSeparableFilter.h
    PtexSeparableKernel.h
    PtexTriangleFilter.h
    PtexTriangleKernel.h
    Ptexture.h
    PtexUtils.h
    PtexWriter.h
)


set(PTEX_SONAME "")
if (NOT WIN32 AND NOT IOS)

    # Evaluate 'soname' from version string

    # replace '_' with '.'
    string(REGEX REPLACE "(_)" "." PTEX_SONAME ${PTEXTURE_VERSION})

    # remove starting 'v' character
    string(REGEX REPLACE "^v" "" PTEX_SONAME ${PTEX_SONAME})
endif()



add_library(Ptex_static STATIC ${SRCS})
set_target_properties(Ptex_static PROPERTIES OUTPUT_NAME Ptex)

add_library(Ptex_dynamic SHARED ${SRCS})

set_target_properties(Ptex_dynamic
    PROPERTIES
        OUTPUT_NAME Ptex
        CLEAN_DIRECT_OUTPUT 1
        SOVERSION ${PTEX_SONAME}
)

target_link_libraries(Ptex_dynamic ${CMAKE_THREAD_LIBS_INIT} ${PLATFORM_LIBRARIES} ${ZLIB_LIBRARIES})

install( TARGETS Ptex_static DESTINATION lib )
install( TARGETS Ptex_dynamic LIBRARY DESTINATION lib )

install( FILES ${PUBLIC_HEADER_FILES}
         DESTINATION include/ptex/
         PERMISSIONS OWNER_READ GROUP_READ WORLD_READ )

