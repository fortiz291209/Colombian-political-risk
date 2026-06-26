cmake_minimum_required(VERSION 3.14)
project(cpr_engine LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_POSITION_INDEPENDENT_CODE ON)

# Find Python and NumPy
find_package(Python3 REQUIRED COMPONENTS Interpreter Development NumPy)

# Build the Python extension module
add_library(cpr_engine MODULE
    cpp/src/pybindings.cpp
)

target_include_directories(cpr_engine PRIVATE
    cpp/include
    ${Python3_INCLUDE_DIRS}
    ${Python3_NumPy_INCLUDE_DIRS}
)

target_link_libraries(cpr_engine PRIVATE
    ${Python3_LIBRARIES}
)

set_target_properties(cpr_engine PROPERTIES
    PREFIX ""
    SUFFIX "${Python3_SOABI}.so"
    OUTPUT_NAME "cpr_engine"
)
