cmake_minimum_required(VERSION 3.14)
project(WinToast VERSION 1.0)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_INCLUDE_CURRENT_DIR ON)

option(WINTOAST_BUILD_EXAMPLES "Build Examples" OFF)

add_library(WinToast SHARED
    src/wintoastlib.cpp
    include/WinToast/WinToast.hpp)
target_include_directories(WinToast
    PUBLIC
        "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>"
        "$<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>"
    )
add_library(WinToast::WinToast ALIAS WinToast)

target_link_libraries(WinToast PUBLIC shlwapi user32)

if(WINTOAST_BUILD_EXAMPLES)
    add_executable(console-example example/console-example/main.cpp)
    target_link_libraries(console-example psapi WinToast::WinToast)

    set(CMAKE_AUTOMOC ON)
    set(CMAKE_AUTORCC ON)
    set(CMAKE_AUTOUIC ON)
    find_package(Qt6 COMPONENTS Core Widgets)
    if(Qt6Core_FOUND AND Qt6Widgets_FOUND)
        add_executable(qt-gui-example
            example/qt-gui-example/main.cpp
            example/qt-gui-example/mainwindow.cpp
            example/qt-gui-example/mainwindow.h)
        target_link_libraries(qt-gui-example Qt::Core Qt::Widgets WinToast::WinToast)
    endif()
endif()

include(GenerateExportHeader)
include(CMakePackageConfigHelpers)
include(GNUInstallDirs)

generate_export_header(WinToast
    EXPORT_FILE_NAME ${CMAKE_CURRENT_BINARY_DIR}/WinToast/WinToast_export.h)

configure_package_config_file(cmake/WinToastConfig.cmake.in ${CMAKE_CURRENT_BINARY_DIR}/WinToastConfig.cmake
    INSTALL_DESTINATION ${LIB_INSTALL_DIR}/cmake/WinToast)

write_basic_package_version_file(${CMAKE_CURRENT_BINARY_DIR}/WinToastConfigVersion.cmake
    COMPATIBILITY SameMajorVersion)

install(TARGETS WinToast
    EXPORT WinToastTargets
    COMPONENT "development"
    EXCLUDE_FROM_ALL
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
)
install(FILES ${CMAKE_CURRENT_BINARY_DIR}/WinToast/WinToast_export.h
    COMPONENT "development"
    EXCLUDE_FROM_ALL
    DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/WinToast)


install(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/include/
    COMPONENT "development"
    EXCLUDE_FROM_ALL
    DESTINATION ${CMAKE_INSTALL_INCLUDEDIR})

install(FILES ${CMAKE_CURRENT_BINARY_DIR}/WinToast/WinToast_export.h
    COMPONENT "development"
    EXCLUDE_FROM_ALL
    DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/WinToast)

install(FILES ${CMAKE_CURRENT_BINARY_DIR}/WinToastConfig.cmake ${CMAKE_CURRENT_BINARY_DIR}/WinToastConfigVersion.cmake
    COMPONENT "development"
    EXCLUDE_FROM_ALL
    DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/WinToast)

export(EXPORT WinToastTargets
    FILE ${CMAKE_CURRENT_BINARY_DIR}/cmake/WinToastTargets.cmake
    NAMESPACE WinToast::
)

install(EXPORT WinToastTargets
    COMPONENT "development"
    EXCLUDE_FROM_ALL
    FILE WinToastTargets.cmake
    NAMESPACE WinToast::
    DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/WinToast
)
