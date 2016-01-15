project(QtZeroConfExample)

cmake_minimum_required(VERSION 3.1)

set(CMAKE_INCLUDE_CURRENT_DIR ON)
set(CMAKE_AUTOMOC ON)

find_package(Qt5Core REQUIRED)
find_package(Qt5Gui REQUIRED)
find_package(Qt5Widgets REQUIRED)
find_package(Qt5Network REQUIRED)

add_executable(QtZeroConfExample main.cpp window.cpp window.h)
target_link_libraries(QtZeroConfExample QtZeroConf Qt5::Core Qt5::Gui Qt5::Widgets Qt5::Network)
