# ---------------------------------------------------------------------------
# src/CMakeLists.txt
# Adds each module as a static library then defines the main executable.
# Subdirectories are ordered leaf-first so inter-target references resolve.
# ---------------------------------------------------------------------------

add_subdirectory(config)
add_subdirectory(diag)
add_subdirectory(spool)
add_subdirectory(stream)
add_subdirectory(hal_bridge)
add_subdirectory(registry)
add_subdirectory(ota)
add_subdirectory(log)
add_subdirectory(janitor)
add_subdirectory(run)
add_subdirectory(hal_service)

# ---------------------------------------------------------------------------
# Main executable
# ---------------------------------------------------------------------------

add_executable(pinnacle-client
    main.cpp
    app.cpp
)

target_include_directories(pinnacle-client PRIVATE
    ${CMAKE_CURRENT_SOURCE_DIR}
    ${CMAKE_SOURCE_DIR}/../Pinnacle-HAL/src
)

target_link_libraries(pinnacle-client PRIVATE
    pinnacle_config
    pinnacle_diag
    pinnacle_hal_bridge
    pinnacle_hal_service
    pinnacle_janitor
    pinnacle_log
    pinnacle_ota
    pinnacle_registry
    pinnacle_run
    pinnacle_spool
    pinnacle_stream
    pinnacle_protos
    spdlog::spdlog
    tomlplusplus::tomlplusplus
    yaml-cpp::yaml-cpp
    SQLite::SQLite3
    ${PINNACLE_GRPC_LIBRARIES}
    protobuf::libprotobuf
)

target_compile_definitions(pinnacle-client PRIVATE
    PINNACLE_CLIENT_VERSION="${PROJECT_VERSION}"
)

set_compiler_warnings(pinnacle-client)
