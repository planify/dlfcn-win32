# Minimum version of CMake that has all features required for this specific file.
cmake_minimum_required(VERSION 3.23)

# Helpers functions for creating config files that can be included by other projects to find and use a package.
include(CMakePackageConfigHelpers)

# Provides install directory variables as defined by the GNU Coding Standards.
include(GNUInstallDirs)

# The project's name and its semantic version number.
project(dlfcn VERSION 1.0.0)

# Only build for Windows.
if (WIN32)
  # Sub-directory to install the project.
  set(PROJECT_INSTALL_SUBDIR "${PROJECT_NAME}-${PROJECT_VERSION}")

  # This library should be statically linked to the project.
  add_library(dlfcn)

  # The library's sources.
  target_sources(${PROJECT_NAME} PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>/src/dlfcn.c)

  # The library's public include directory.
  target_include_directories(${PROJECT_NAME} PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>/src)

  # Set the installation's include directory.
  target_include_directories(${PROJECT_NAME} PUBLIC
    $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>/${PROJECT_INSTALL_SUBDIR})

  # Headers to install with the library.
  target_sources(${PROJECT_NAME} PUBLIC FILE_SET HEADERS
    BASE_DIRS
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>/src

    FILES
    src/dlfcn.h)

  # Specify the install target and its export name.
  install(
    TARGETS
    ${PROJECT_NAME}

    EXPORT
    ${PROJECT_NAME}-targets

    COMPONENT
    Development

    DESTINATION
    ${CMAKE_INSTALL_LIBDIR}/${PROJECT_INSTALL_SUBDIR}

    FILE_SET
    HEADERS

    COMPONENT
    Development

    DESTINATION
    ${CMAKE_INSTALL_INCLUDEDIR}/${PROJECT_INSTALL_SUBDIR})

  # Install the project's export details.
  install(
    EXPORT
    ${PROJECT_NAME}-targets

    FILE
    ${PROJECT_NAME}-targets.cmake

    DESTINATION
    ${CMAKE_INSTALL_LIBDIR}/cmake/${PROJECT_INSTALL_SUBDIR})

  configure_package_config_file(
    ${CMAKE_CURRENT_SOURCE_DIR}/config.cmake.in
    ${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}-config.cmake

    INSTALL_DESTINATION
    ${CMAKE_INSTALL_LIBDIR}/cmake/${PROJECT_INSTALL_SUBDIR})

  install(
    FILES
    ${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}-config.cmake
    ${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}-config-version.cmake

    DESTINATION
    ${CMAKE_INSTALL_LIBDIR}/cmake/${PROJECT_INSTALL_SUBDIR})

  # Target version and compatibility.
  set_property(TARGET ${PROJECT_NAME} PROPERTY VERSION ${PROJECT_VERSION})
  set_property(TARGET ${PROJECT_NAME} PROPERTY SOVERSION ${PROJECT_VERSION_MAJOR})
  set_property(TARGET ${PROJECT_NAME} PROPERTY INTERFACE_${PROJECT_NAME}_MAJOR_VERSION ${PROJECT_VERSION_MAJOR})
  set_property(TARGET ${PROJECT_NAME} APPEND PROPERTY COMPATIBLE_INTERFACE_STRING ${PROJECT_NAME}_MAJOR_VERSION)

  # Generate the version file for the config file.
  write_basic_package_version_file(
    ${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}-config-version.cmake

    VERSION
    ${PROJECT_VERSION}

    COMPATIBILITY
    SameMajorVersion)
endif ()
