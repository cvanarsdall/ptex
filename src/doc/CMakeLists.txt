find_package ( Doxygen )


if (DOXYGEN_FOUND)

    configure_file(${CMAKE_CURRENT_SOURCE_DIR}/Doxyfile_cmake ${CMAKE_CURRENT_BINARY_DIR}/Doxyfile_cmake @ONLY)

    add_custom_target (doc ALL
        ${DOXYGEN_EXECUTABLE} ${CMAKE_CURRENT_BINARY_DIR}/Doxyfile_cmake
        WORKING_DIRECTORY 
            ${CMAKE_SOURCE_DIR}/doc
        COMMENT 
            "Generating API documentation with Doxygen" VERBATIM
    )

    install( 
        DIRECTORY
            ${CMAKE_CURRENT_BINARY_DIR}/ptex
        DESTINATION 
            doc
        FILE_PERMISSIONS 
            OWNER_READ OWNER_WRITE 
            GROUP_READ 
            WORLD_READ 
        DIRECTORY_PERMISSIONS 
            OWNER_READ OWNER_WRITE OWNER_EXECUTE
            GROUP_READ GROUP_EXECUTE 
            WORLD_READ WORLD_EXECUTE
    )

endif (DOXYGEN_FOUND)
