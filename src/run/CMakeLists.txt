add_library(pinnacle_run STATIC RunOrchestrator.cc
) target_include_directories(pinnacle_run PRIVATE ${CMAKE_SOURCE_DIR}/src ${CMAKE_SOURCE_DIR}/../Pinnacle-HAL/src
) target_link_libraries(pinnacle_run PRIVATE pinnacle_hal_bridge pinnacle_protos spdlog::spdlog
)
