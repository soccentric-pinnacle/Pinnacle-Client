add_library(pinnacle_log STATIC
    LogCollector.cc
)

target_include_directories(pinnacle_log PRIVATE
    ${CMAKE_SOURCE_DIR}/src
)

target_link_libraries(pinnacle_log PRIVATE
    pinnacle_protos
    spdlog::spdlog
)
