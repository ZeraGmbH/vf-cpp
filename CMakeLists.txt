cmake_minimum_required(VERSION 3.1)

project(vf-cpp LANGUAGES CXX)

set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)

set(VfCpp_VERSION_MAJOR "0")
set(VfCpp_VERSION_MINOR "0")
set(VfCpp_VERSION_PATCH "1")
set(PROJECT_VERSION "${VfCpp_VERSION_MAJOR}.${VfCpp_VERSION_MINOR}.${VfCpp_VERSION_PATCH}")

include(FeatureSummary)
include(GNUInstallDirs)
include(GenerateExportHeader)

find_package(Qt5 COMPONENTS Core CONFIG REQUIRED)
find_package(VeinFramework REQUIRED)

file(GLOB SOURCES 
    ${PROJECT_SOURCE_DIR}/*.cpp
    )

file(GLOB HEADER
    ${PROJECT_SOURCE_DIR}/*.h
    )

add_library(VfCpp SHARED
    ${SOURCES}
    ${HEADER}
    )

target_link_libraries(VfCpp 
    PUBLIC
    Qt5::Core
    Zera::VfHelpers
    Zera::VfEvent
    Zera::VfComponent
    )

set_target_properties(VfCpp PROPERTIES VERSION ${PROJECT_VERSION})
set_target_properties(VfCpp PROPERTIES SOVERSION ${VfCpp_VERSION_MAJOR})

generate_export_header(VfCpp)
install(FILES
    ${CMAKE_CURRENT_BINARY_DIR}/vfcpp_export.h
    DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/VfCpp
    )

target_include_directories(VfCpp
    PUBLIC
    $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}/VfCpp>
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
    $<BUILD_INTERFACE:${CMAKE_CURRENT_BINARY_DIR}>
    )

install(TARGETS VfCpp
    EXPORT VfCppExport
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    )

include(CMakePackageConfigHelpers)
write_basic_package_version_file(
    VfCppConfigVersion.cmake
    VERSION ${PACKAGE_VERSION}
    COMPATIBILITY SameMajorVersion
    )

configure_file(VfCppConfig.cmake.in VfCppConfig.cmake @ONLY)

install(FILES "${CMAKE_CURRENT_BINARY_DIR}/VfCppConfig.cmake"
    "${CMAKE_CURRENT_BINARY_DIR}/VfCppConfigVersion.cmake"
    DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/VfCpp
    )

install(EXPORT VfCppExport
    FILE VfCppTargets.cmake
    NAMESPACE VeinMeta::
    DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/VfCpp
    )

install(
    FILES ${HEADER}
    DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/VfCpp
    )

feature_summary(WHAT ALL FATAL_ON_MISSING_REQUIRED_PACKAGES)
