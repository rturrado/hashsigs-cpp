cmake_minimum_required(VERSION 3.15)
project(hashsigs-cpp VERSION 0.0.1 LANGUAGES CXX)

# Set C++ standard
set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

# Options
option(BUILD_TESTS "Build test suite" ON)  # Enable tests
option(BUILD_DOCS "Build docs" OFF)
option(BUILD_SHARED_LIBS "Build shared libraries" OFF)

# Preprocessing
get_filename_component(RESOURCES_FOLDER_PATH ${CMAKE_CURRENT_SOURCE_DIR}/res ABSOLUTE)
configure_file(
    ${CMAKE_CURRENT_SOURCE_DIR}/include/hashsigs-cpp/resources.hpp.in
    ${CMAKE_CURRENT_SOURCE_DIR}/include/hashsigs-cpp/resources.hpp
)

# Set output directories
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)

##############
# Dependencies
##############
include(FetchContent)
include(FindPackageMessage)

# Add OpenSSL
find_package(OpenSSL REQUIRED)

# Add fmt
FetchContent_Declare(
    fmt
    GIT_REPOSITORY https://github.com/fmtlib/fmt
    GIT_TAG 407c905e45ad75fc29bf0f9bb7c5c2fd3475976f)  # 12.1.0
FetchContent_MakeAvailable(fmt)


################
# Library target
################
add_library(hashsigs
    ${CMAKE_CURRENT_SOURCE_DIR}/src/wotsplus.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/src/keccak.cpp
)

target_include_directories(hashsigs PUBLIC
    ${CMAKE_CURRENT_SOURCE_DIR}/include
)

target_link_libraries(hashsigs PRIVATE
    fmt::fmt
    OpenSSL::SSL
    OpenSSL::Crypto
)

##########
# CLI tool
##########
add_executable(hashsigs_cli
    src/hashsigs_cli.cpp
)

target_link_libraries(hashsigs_cli PRIVATE
    hashsigs
)

###############
# Install rules
###############
include(GNUInstallDirs)

install(
    TARGETS hashsigs
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
    RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
)

install(
    DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/include/
    DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
    FILES_MATCHING PATTERN "*.hpp" PATTERN "*.h"
)

#######
# Tests
#######
if(BUILD_TESTS)
    enable_testing()
    add_subdirectory(tests)
endif()

###############
# Documentation
###############
if(BUILD_DOCS)
    message(STATUS "Building docs...")
    find_package(Doxygen)
    if(Doxygen_FOUND)
        set(DOXYGEN_IN ${CMAKE_CURRENT_SOURCE_DIR}/docs/Doxyfile.in)
        set(DOXYGEN_OUT ${CMAKE_CURRENT_BINARY_DIR}/Doxyfile)

        configure_file(${DOXYGEN_IN} ${DOXYGEN_OUT} @ONLY)

        add_custom_target(docs
            COMMAND ${DOXYGEN_EXECUTABLE} ${DOXYGEN_OUT}
            WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}
            COMMENT "Generating API documentation with Doxygen..."
            VERBATIM
        )
    else()
        message(STATUS "Doxygen not found.")
    endif()
endif()
