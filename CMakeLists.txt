cmake_minimum_required(VERSION 3.20 FATAL_ERROR)

set(CMAKE_CXX_COMPILER dpcpp)
set(CMAKE_CXX_STANDARD 17)
project(dpcpp CXX)

add_subdirectory(fmt)

# Standard executable, linking external library
add_executable(try try.cc)
target_link_libraries(try fmt::fmt)

# Linking to the header-only version of this library works
add_executable(try_header_only try.cc)
target_link_libraries(try_header_only fmt::fmt-header-only)

# As does adding the -fintelfpga flag
add_executable(try_intelfpga try.cc)
target_link_options(try_intelfpga PRIVATE -fintelfpga)
target_link_libraries(try_intelfpga fmt::fmt)