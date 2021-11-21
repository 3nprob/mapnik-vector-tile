add_executable(tileinfo tileinfo.cpp)
target_link_libraries(tileinfo PRIVATE mapnik::mapnik-vector-tile)
