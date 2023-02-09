cmake_minimum_required(VERSION 3.8.2)

project(sioyek VERSION 2.0.0)

# Find includes in the build directories
set(CMAKE_INCLUDE_CURRENT_DIR ON)

# Turn on automatic invocation of the MOC, UIC & RCC
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTOUIC ON)
set(CMAKE_AUTORCC ON)

# There may be a way to tell up front if Qt5 is going to be found, but I haven't found
# a foolproof way to do it yet, so settle for the default error message for now.
#if(NOT CMAKE_PREFIX_PATH AND NOT Qt5Widgets_DIR)
#    message(WARNING "CMAKE_PREFIX_PATH is not defined, so find_package may not work. Set the CMAKE_PREFIX_PATH "
#            "environment variable to the install prefix of Qt 5, either on the command line as "
#            "-DCMAKE_PREFIX_PATH=\"path/to/Qt5/lib/cmake\" or with set(CMAKE_PREFIX_PATH path/to/Qt5/lib/cmake)")
#endif(NOT CMAKE_PREFIX_PATH AND NOT Qt5Widgets_DIR)

# Add a compiler flag
if(MSVC)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} /std:c++17")
else()
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++17")
endif()

# Find the QtWidgets library
find_package(Qt5 REQUIRED COMPONENTS Core OpenGL Gui Widgets Network 3DInput OpenGLExtensions) #)
find_package(ZLIB REQUIRED)
find_package(libmupdf CONFIG REQUIRED)
find_package(libresources CONFIG REQUIRED)
find_package(unofficial-sqlite3 CONFIG REQUIRED)
find_package(unofficial-gumbo CONFIG REQUIRED)
find_package(Freetype REQUIRED)

file(GLOB HEADERS ${PROJECT_SOURCE_DIR}/include/*.h ${PROJECT_SOURCE_DIR}/include/*.hpp)
file(GLOB SRCS ${PROJECT_SOURCE_DIR}/src/*.cpp)

file(GLOB SYNC_HEADERS ${PROJECT_SOURCE_DIR}/synctex/*.h)
file(GLOB SYNC_SRCS ${PROJECT_SOURCE_DIR}/synctex/*.c)

# Tell CMake to create the helloworld executable
add_executable(sioyek ${SRCS} ${HEADERS} ${SYNC_SRCS} ${SYNC_HEADERS})

target_include_directories(sioyek PUBLIC include)
# Add the Qt5 Widgets for linking
target_link_libraries(sioyek 
    Qt5::Core
    Qt5::OpenGL
    Qt5::Gui
    Qt5::Widgets
    Qt5::Network
    Qt5::3DInput
    Qt5::OpenGLExtensions
    ZLIB::ZLIB
    unofficial::libmupdf::libmupdf
    unofficial::sqlite3::sqlite3
    unofficial::gumbo::gumbo
    Freetype::Freetype
    unofficial::libresources::libresources
    opengl32.lib
)

target_compile_definitions(sioyek PRIVATE QT_3DINPUT_LIB QT_OPENGL_LIB QT_OPENGLEXTENSIONS_LIB QT_WIDGETS_LIB)

if(WIN32)
    target_compile_definitions(sioyek PRIVATE _CRT_SECURE_NO_WARNINGS _CRT_NONSTDC_NO_DEPRECATE)
endif()