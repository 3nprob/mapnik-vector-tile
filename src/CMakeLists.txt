find_package(Protobuf REQUIRED)

add_library(mapnik-vector-tile STATIC)
add_library(mapnik::mapnik-vector-tile ALIAS mapnik-vector-tile)
target_include_directories(mapnik-vector-tile PUBLIC  
    $<BUILD_INTERFACE:${CMAKE_CURRENT_BINARY_DIR}>
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
    $<INSTALL_INTERFACE:include>
)
target_link_libraries(mapnik-vector-tile PUBLIC 
    mapnik::mapnik
    protobuf::libprotoc 
)
target_compile_definitions(mapnik-vector-tile 
    PUBLIC 
        MAPNIK_VECTOR_TILE_LIBRARY
        $<$<BOOL:${USE_SSE_MATH}>:SSE_MATH>
    PRIVATE
        _USE_MATH_DEFINES
)
set_target_properties(mapnik-vector-tile PROPERTIES
    CXX_STANDARD 14
    CXX_STANDARD_REQUIRED ON 
    CXX_EXTENSIONS OFF
)

protobuf_generate_cpp(PROTO_SRCS PROTO_HDRS "${PROJECT_SOURCE_DIR}/proto/vector_tile.proto")

target_sources(mapnik-vector-tile PRIVATE
    ${PROTO_SRCS} ${PROTO_HDRS}
    boost_geometry_adapters.hpp
    vector_tile_composite.hpp
    vector_tile_compression.cpp
    vector_tile_compression.hpp
    vector_tile_config.hpp
    vector_tile_datasource_pbf.cpp
    vector_tile_datasource_pbf.hpp
    vector_tile_douglas_peucker.hpp
    vector_tile_featureset_pbf.cpp
    vector_tile_featureset_pbf.hpp
    vector_tile_geometry_clipper.hpp
    vector_tile_geometry_decoder.cpp
    vector_tile_geometry_decoder.hpp
    vector_tile_geometry_encoder_pbf.cpp
    vector_tile_geometry_encoder_pbf.hpp
    vector_tile_geometry_feature.hpp
    vector_tile_geometry_simplifier.hpp
    vector_tile_is_valid.hpp
    vector_tile_layer.cpp
    vector_tile_layer.hpp
    vector_tile_load_tile.hpp
    vector_tile_merc_tile.hpp
    vector_tile_processor.cpp
    vector_tile_processor.hpp
    vector_tile_projection.hpp
    vector_tile_raster_clipper.cpp
    vector_tile_raster_clipper.hpp
    vector_tile_strategy.hpp
    vector_tile_tile.cpp
    vector_tile_tile.hpp
)
