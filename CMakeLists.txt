cmake_minimum_required(VERSION 3.15 FATAL_ERROR)

project(
    tryCmake
    VERSION 1.0.0
    DESCRIPTION "A standard C++17 project template with CMake"
    HOMEPAGE_URL "https://github.com/acgzhr/tryCmake.git"
    LANGUAGES CXX
)

# Use CXX_STANDARD_REQUIRED to enforce the standard (no fallback to older versions)
# Use CXX_EXTENSIONS OFF to disable compiler-specific extensions (-std=c++17 instead of -std=gnu++17)
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

# Define source and header directories
set(PROJECT_SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/src)
set(PROJECT_INCLUDE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/include)
set(PROJECT_TEST_DIR ${CMAKE_CURRENT_SOURCE_DIR}/tests)

# Add include directories (make headers available to the compiler)
include_directories(
    PUBLIC ${PROJECT_INCLUDE_DIR}  # Public headers (visible to other targets)
    PRIVATE ${PROJECT_SOURCE_DIR}  # Private headers (only for this project's sources)
)

# Create library target (static by default; use SHARED for dynamic library)
# Split sources into library and executable for better testability
# add_library(
#     ${PROJECT_NAME}_core
#     SHARED
#     # Add your source files here
#     # ${PROJECT_SOURCE_DIR}/utils/math_helpers.cpp
#     # ${PROJECT_SOURCE_DIR}/core/application.cpp
#     # ${PROJECT_SOURCE_DIR}/core/logger.cpp
# )

# Set library properties
# set_target_properties(
#     ${PROJECT_NAME}_core
#     PROPERTIES
#     VERSION ${PROJECT_VERSION}
#     SOVERSION ${PROJECT_VERSION_MAJOR}  # For shared library versioning
#     OUTPUT_NAME ${PROJECT_NAME}        # Simplify output name
# )

# Create executable target (links against the core library)
add_executable(
    ${PROJECT_NAME}_app
    ${PROJECT_SOURCE_DIR}/main.cpp
)

# Link executable to core library
# target_link_libraries(
#     ${PROJECT_NAME}_app
#     PRIVATE ${PROJECT_NAME}_core
# )

# --------------------------
# Installation (Local bin)
# --------------------------

# Set local installation prefix (project root's bin directory)
set(CMAKE_INSTALL_PREFIX ${CMAKE_CURRENT_SOURCE_DIR}/bin)

# Install dynamic library and headers to local bin
install(
    TARGETS ${PROJECT_NAME}_app
    ARCHIVE DESTINATION lib          # Static lib fallback (if needed)
    LIBRARY DESTINATION lib          # Dynamic lib (Linux/macOS)
    RUNTIME DESTINATION .            # DLL (Windows) - directly in bin
    INCLUDES DESTINATION include     # Install headers to bin/include
)

# Install headers (preserve directory structure)
install(
    DIRECTORY ${PROJECT_INCLUDE_DIR}/${PROJECT_NAME}
    DESTINATION include
    FILES_MATCHING PATTERN "*.hpp" PATTERN "*.h"
)

# Export CMake config for find_package() support
# install(
#     EXPORT ${PROJECT_NAME}_Targets
#     FILE ${PROJECT_NAME}Config.cmake
#     NAMESPACE ${PROJECT_NAME}::
#     DESTINATION ${CMAKE_INSTALL_DATAROOTDIR}/${PROJECT_NAME}/cmake
# )

# --------------------------
# Testing (Optional)
# --------------------------

# Enable testing if tests directory exists
# if(EXISTS ${PROJECT_TEST_DIR} AND IS_DIRECTORY ${PROJECT_TEST_DIR})
#     enable_testing()
#     add_subdirectory(${PROJECT_TEST_DIR})
#     message(STATUS "Tests enabled - added test subdirectory")
# else()
#     message(STATUS "Tests disabled - no test directory found")
# endif()

# --------------------------
# Build Type Configuration
# --------------------------

# Set default build type to Debug if not specified
if(NOT CMAKE_BUILD_TYPE AND NOT CMAKE_CONFIGURATION_TYPES)
    set(CMAKE_BUILD_TYPE "Debug" CACHE STRING "Default build type: Debug" FORCE)
    # Provide build type options in CMake GUI
    set_property(CACHE CMAKE_BUILD_TYPE PROPERTY STRINGS "Debug" "Release" "RelWithDebInfo" "MinSizeRel")
endif()

# --------------------------
# Status Messages
# --------------------------

message(STATUS "========================================")
message(STATUS "Project: ${PROJECT_NAME} v${PROJECT_VERSION}")
message(STATUS "CMake Version: ${CMAKE_VERSION}")
message(STATUS "C++ Compiler: ${CMAKE_CXX_COMPILER_ID} ${CMAKE_CXX_COMPILER_VERSION}")
message(STATUS "C++ Standard: ${CMAKE_CXX_STANDARD} (Required: ${CMAKE_CXX_STANDARD_REQUIRED})")
message(STATUS "Build Type: ${CMAKE_BUILD_TYPE}")
message(STATUS "Install Prefix: ${CMAKE_INSTALL_PREFIX}")
message(STATUS "========================================")
