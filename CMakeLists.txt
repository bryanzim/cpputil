cmake_minimum_required(VERSION 3.11)
project(dechamps_cpputil LANGUAGES CXX)

# clang-cl does not implement MSVC /analyze.
# /wd4458 and /wd4456 suppress shadowing warnings from lambdas that intentionally
# reuse the name of the variable they initialize.
set(_warning_options /WX /W4 /permissive- /wd4458 /wd4456)
if(NOT (CMAKE_CXX_COMPILER_ID STREQUAL "Clang" AND CMAKE_CXX_SIMULATE_ID STREQUAL "MSVC"))
	list(APPEND _warning_options /analyze)
endif()
add_compile_options(${_warning_options})

include(TestBigEndian)
TEST_BIG_ENDIAN(DECHAMPS_CPPUTIL_BIG_ENDIAN)
configure_file("${CMAKE_CURRENT_LIST_DIR}/endian.in.h" endian.h)
add_library(endian INTERFACE)
target_include_directories(endian
	INTERFACE "$<BUILD_INTERFACE:${CMAKE_CURRENT_BINARY_DIR}>"
	INTERFACE "$<INSTALL_INTERFACE:include>"
)

add_library(exception STATIC exception.cpp)
target_include_directories(exception INTERFACE "$<INSTALL_INTERFACE:include>")

add_library(find INTERFACE)
target_include_directories(find INTERFACE "$<INSTALL_INTERFACE:include>")

add_library(string INTERFACE)
target_include_directories(string INTERFACE "$<INSTALL_INTERFACE:include>")

install(FILES "${CMAKE_CURRENT_BINARY_DIR}/endian.h" exception.h find.h string.h DESTINATION include/dechamps_cpputil)
install(TARGETS endian exception find string EXPORT dechamps_cpputil-config)
install(EXPORT dechamps_cpputil-config NAMESPACE dechamps_cpputil:: DESTINATION lib/cmake/dechamps_cpputil)
