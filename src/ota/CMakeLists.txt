add_library(pinnacle_ota STATIC OtaClient.cc
) target_include_directories(pinnacle_ota PRIVATE ${CMAKE_SOURCE_DIR}/src
) target_link_libraries(pinnacle_ota PRIVATE pinnacle_protos ${PINNACLE_GRPC_LIBRARIES} spdlog::spdlog
)
