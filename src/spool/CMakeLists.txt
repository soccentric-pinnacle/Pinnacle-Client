add_library(pinnacle_spool STATIC
    Spool.cc
)

target_include_directories(pinnacle_spool PRIVATE
    ${CMAKE_SOURCE_DIR}/src
)

target_link_libraries(pinnacle_spool PRIVATE
    pinnacle_protos
    SQLite::SQLite3
    spdlog::spdlog
)
