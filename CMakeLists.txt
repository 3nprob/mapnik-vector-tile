cmake_minimum_required(VERSION 3.15)
file(READ package.json package_json)
string(JSON json_project_version GET ${package_json} "version")
project(mapnik-vector-tile
    VERSION ${json_project_version}
    HOMEPAGE_URL "https://github.com/mathisloge/mapnik-vector-tile" 
    DESCRIPTION "Mapnik implemention of Mapbox Vector Tile specification" 
    LANGUAGES CXX 
)

include(CTest)
include(GNUInstallDirs)
option(USE_SSE_MATH "use SSE instructions" OFF)
option(BUILD_EXAMPLES "build example application" OFF)
option(BUILD_BIN "build helper binary" OFF)
option(MAPNIK_VECTOR_TILE_ENABLE_INSTALL "disable install step" OFF)
set(MAPBOX_WAGYU_INCLUDE_DIRS "" CACHE PATH "include dir for mapbox/wagyu")

set(m_bin_dir ${CMAKE_INSTALL_BINDIR} CACHE STRING "Install directory for binaries")
set(m_lib_dir ${CMAKE_INSTALL_LIBDIR} CACHE STRING "Install directory for libraries")
set(m_inc_dir ${CMAKE_INSTALL_INCLUDEDIR} CACHE STRING "Install directory for the headers")
set(m_cmake_dir ${MAPNIK_LIB_DIR}/cmake/mapnikVectorTile CACHE STRING "Install directory of the cmake targets")

find_package(mapnik CONFIG REQUIRED)
find_package(Protobuf REQUIRED)

add_subdirectory(src)
if(BUILD_BIN)
    add_subdirectory(bin)
endif()
if(BUILD_EXAMPLES)
    add_subdirectory(examples)
endif()
if(BUILD_TESTING)
    add_subdirectory(test)
endif()

if(MAPNIK_VECTOR_TILE_ENABLE_INSTALL)
    include(CMakePackageConfigHelpers)

    install(TARGETS mapnik-vector-tile
        EXPORT mapnikVectorTileTargets
        INCLUDES DESTINATION ${m_inc_dir}
        RUNTIME DESTINATION ${m_bin_dir}
            COMPONENT mapnikVectorTileRuntime
        LIBRARY DESTINATION ${m_lib_dir}
            COMPONENT mapnikVectorTileRuntime
            NAMELINK_COMPONENT mapnikVectorTileDevelopment
        ARCHIVE DESTINATION ${m_lib_dir}
            COMPONENT mapnikVectorTileDevelopment
    )
    install(DIRECTORY src/ DESTINATION ${m_inc_dir}
        FILES_MATCHING 
            PATTERN "*.hpp"
            PATTERN "*.ipp"
    )

    # export mapnik configuration
    write_basic_package_version_file(
        "${CMAKE_CURRENT_BINARY_DIR}/mapnikVectorTileConfigVersion.cmake"
        VERSION ${MAPNIK_VERSION}
        COMPATIBILITY ExactVersion
    )
    configure_package_config_file(cmake/Config.cmake.in
        "${CMAKE_CURRENT_BINARY_DIR}/mapnikVectorTileConfig.cmake"
        INSTALL_DESTINATION ${m_cmake_dir}
        NO_CHECK_REQUIRED_COMPONENTS_MACRO
    )

    install(
        FILES
            "${CMAKE_CURRENT_BINARY_DIR}/mapnikVectorTileConfig.cmake"
            "${CMAKE_CURRENT_BINARY_DIR}/mapnikVectorTileConfigVersion.cmake"
        DESTINATION ${m_cmake_dir}
    )

    install(EXPORT mapnikVectorTileTargets
        DESTINATION ${m_cmake_dir}
        FILE mapnikVectorTile.cmake
        NAMESPACE mapnik::
    )
endif()