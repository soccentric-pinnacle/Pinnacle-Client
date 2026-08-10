add_library(pinnacle_hal_service STATIC
    HalServiceImpl.cc
)

target_include_directories(pinnacle_hal_service PRIVATE
    ${CMAKE_SOURCE_DIR}/src
    ${CMAKE_SOURCE_DIR}/../Pinnacle-HAL/src
)

target_link_libraries(pinnacle_hal_service PRIVATE
    pinnacle_registry
    pinnacle_hal_bridge
    pinnacle_protos
    ${PINNACLE_GRPC_LIBRARIES}
    protobuf::libprotobuf
    spdlog::spdlog
)
