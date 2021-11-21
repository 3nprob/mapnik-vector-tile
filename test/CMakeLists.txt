include(FetchContent)
FetchContent_Declare(
  Catch2
  GIT_REPOSITORY https://github.com/catchorg/Catch2.git
  GIT_TAG        v2.13.7)
FetchContent_MakeAvailable(Catch2)
include("${Catch2_SOURCE_DIR}/contrib/Catch.cmake")

mapnik_find_plugin_dir(MAPNIK_PLUGIN_DIR)

add_executable(vector-tile-test)
target_link_libraries(vector-tile-test PRIVATE
    Catch2::Catch2 
    mapnik::mapnik-vector-tile
    mapnik::json
    mapnik::wkt
)
target_compile_definitions(vector-tile-test 
    PRIVATE
        _USE_MATH_DEFINES
        MAPNIK_PLUGINDIR="${MAPNIK_PLUGIN_DIR}"
)
set_target_properties(vector-tile-test PROPERTIES
    CXX_STANDARD 14
    CXX_STANDARD_REQUIRED ON 
    CXX_EXTENSIONS OFF
)
target_include_directories(vector-tile-test PRIVATE 
    "${CMAKE_CURRENT_SOURCE_DIR}"
    utils
)

catch_discover_tests(vector-tile-test)

file(COPY data DESTINATION "${CMAKE_CURRENT_BINARY_DIR}/test")
file(COPY geometry-test-data DESTINATION "${CMAKE_CURRENT_BINARY_DIR}/test")
file(COPY fixtures DESTINATION "${CMAKE_CURRENT_BINARY_DIR}/test")

target_sources(vector-tile-test PRIVATE
    geometry_visual_test.cpp
    raster_tile.cpp
    test_main.cpp
    test_utils.cpp
    test_utils.hpp
    vector_tile_pbf.cpp
    vector_tile_projection.cpp
    vector_tile_rasterize.cpp
    vector_tile.cpp
    utils/decoding_util.cpp
    utils/decoding_util.hpp
    utils/encoding_util.cpp
    utils/encoding_util.hpp
    utils/geom_to_wkt.cpp
    utils/geom_to_wkt.hpp
    utils/geometry_equal.hpp
    utils/round_trip.cpp
    utils/round_trip.hpp
    system/encode_and_datasource_decode.cpp
    system/encode_and_decode.cpp
    system/processor_and_datasource.cpp
    system/remove_repeated_point.cpp
    system/round_trip_fill_type.cpp
    system/round_trip_simplification.cpp
    system/round_trip.cpp
    unit/composite/vector.cpp
    unit/compression/compression.cpp
    unit/datasource-pbf/from_layer.cpp
    unit/decoding/linestring.cpp
    unit/decoding/point.cpp
    unit/decoding/polygon_scaling.cpp
    unit/decoding/polygon.cpp
    unit/encoding/geometry_to_feature_pbf.cpp
    unit/encoding/linestring_pbf.cpp
    unit/encoding/point_pbf.cpp
    unit/encoding/polygon_pbf.cpp
    unit/is_valid/feature_is_valid.cpp
    unit/is_valid/value_is_valid.cpp
    unit/layer_impl/layer.cpp
    unit/load/merge.cpp
    unit/processor/reprojection_error.cpp
    unit/processor/scale_denom_filter.cpp
    unit/processor/variables.cpp
    unit/tile_impl/tile.cpp
)
