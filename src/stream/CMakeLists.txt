add_library(pinnacle_stream STATIC
    StreamClient.cc
)

target_include_directories(pinnacle_stream PRIVATE
    ${CMAKE_SOURCE_DIR}/src
)

target_link_libraries(pinnacle_stream PRIVATE
    pinnacle_protos
    ${PINNACLE_GRPC_LIBRARIES}
    spdlog::spdlog
)
