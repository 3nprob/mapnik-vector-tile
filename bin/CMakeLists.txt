add_executable(vtile vtile-edit.cpp)
target_link_libraries(vtile PRIVATE mapnik::mapnik-vector-tile)
