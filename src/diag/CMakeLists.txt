add_library(pinnacle_diag STATIC
    DiagSocket.cc
)

target_include_directories(pinnacle_diag PRIVATE
    ${CMAKE_SOURCE_DIR}/src
)

target_link_libraries(pinnacle_diag PRIVATE
    spdlog::spdlog
)
