add_library(pinnacle_janitor STATIC Janitor.cc
) target_include_directories(pinnacle_janitor PRIVATE ${CMAKE_SOURCE_DIR}/src
) target_link_libraries(pinnacle_janitor PRIVATE pinnacle_protos spdlog::spdlog
)
