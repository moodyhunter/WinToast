cmake_minimum_required(VERSION 3.14)
project (WinToast)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_INCLUDE_CURRENT_DIR ON)

option(WINTOAST_BUILD_EXAMPLES "Build Examples" OFF)

include_directories(${PROJECT_SOURCE_DIR}/src/)
set(SRC_FILES
    ${PROJECT_SOURCE_DIR}/src/wintoastlib.cpp
    ${PROJECT_SOURCE_DIR}/src/wintoastlib.h)
add_library(WinToast STATIC ${SRC_FILES})
add_library(WinToast::WinToast ALIAS WinToast)

if(WINTOAST_BUILD_EXAMPLES)
    add_executable(console-example
        ${PROJECT_SOURCE_DIR}/example/console-example/main.cpp)
    target_link_libraries(console-example WinToast psapi)
    add_dependencies(console-example WinToast::WinToast)

    set(CMAKE_AUTOMOC ON)
    set(CMAKE_AUTORCC ON)
    set(CMAKE_AUTOUIC ON)
    find_package(Qt6 COMPONENTS Core Widgets)
    if(Qt6Core_FOUND AND Qt6Widgets_FOUND)
        file(GLOB QT_GUI_SRC_FILES
            ${PROJECT_SOURCE_DIR}/example/qt-gui-example/WinToastExample/main.cpp
            ${PROJECT_SOURCE_DIR}/example/qt-gui-example/WinToastExample/mainwindow.cpp
            ${PROJECT_SOURCE_DIR}/example/qt-gui-example/WinToastExample/mainwindow.h
            )

        add_executable(qt-gui-example ${QT_GUI_SRC_FILES})
        target_include_directories(qt-gui-example PRIVATE ${PROJECT_SOURCE_DIR}/qt-gui-example/)
        target_link_libraries(qt-gui-example Qt::Core Qt::Widgets WinToast::WinToast)
    endif()
endif()
