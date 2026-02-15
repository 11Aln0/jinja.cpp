cmake_minimum_required(VERSION 3.10)
project(jinja_cpp VERSION 0.0.1 LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# 选项：使用外部 nlohmann_json 还是内嵌的
option(JINJA_USE_EXTERNAL_JSON "Use external nlohmann_json instead of bundled one" OFF)
option(UJSON_USE_RAPIDJSON "Use RapidJSON backend" OFF)

# Header-only Library
add_library(jinja INTERFACE)

# 始终需要 include 自身目录（jinja.hpp 等）
target_include_directories(jinja INTERFACE 
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
)

if(UJSON_USE_RAPIDJSON)
    target_compile_definitions(jinja INTERFACE UJSON_USE_RAPIDJSON)
    target_include_directories(jinja INTERFACE 
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/third_party/rapidjson/include>
    )
elseif(JINJA_USE_EXTERNAL_JSON)
    # 使用外部 nlohmann_json，不 include third_party
    find_package(nlohmann_json REQUIRED)
    target_link_libraries(jinja INTERFACE nlohmann_json::nlohmann_json)
else()
    # 使用内嵌的 third_party
    target_include_directories(jinja INTERFACE 
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/third_party>
    )
endif()

# Test
option(JINJA_BUILD_TESTS "Build tests" ON)
if(JINJA_BUILD_TESTS)
    enable_testing()
    add_executable(test_main tests/test_main.cpp)
    target_link_libraries(test_main jinja)
endif()