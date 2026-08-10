add_library(pinnacle_hal_bridge STATIC
    ResultSerializer.cc
)

target_include_directories(pinnacle_hal_bridge PRIVATE
    ${CMAKE_SOURCE_DIR}/src
    ${CMAKE_SOURCE_DIR}/../Pinnacle-HAL/src
)

target_link_libraries(pinnacle_hal_bridge PRIVATE
    pinnacle_protos
    spdlog::spdlog
)
