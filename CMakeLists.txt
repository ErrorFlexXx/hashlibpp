cmake_minimum_required(VERSION 3.9)

set(CMAKE_WINDOWS_EXPORT_ALL_SYMBOLS ON) # Export symbols on windows

project(hashlibpp VERSION 0.0.1 DESCRIPTION "Library to create a cryptographic checksum")

file(GLOB ${PROJECT_NAME}_HEADER
    src/*.h
)

file(GLOB ${PROJECT_NAME}_SOURCES
    src/*.cpp
)

add_library(${PROJECT_NAME} STATIC
    ${${PROJECT_NAME}_HEADER}
    ${${PROJECT_NAME}_SOURCES}
)

set_target_properties(${PROJECT_NAME} PROPERTIES
    VERSION ${PROJECT_VERSION}
    PUBLIC_HEADER "${${PROJECT_NAME}_PUBLIC_HEADER}"
)

if(MSVC)
	target_compile_options(${PROJECT_NAME} PRIVATE /W3 /WX /D_CRT_SECURE_NO_WARNINGS /EHsc)
else()
	target_compile_options(${PROJECT_NAME} PRIVATE -Wall -Wextra -pedantic -Werror -Wno-unknown-pragmas -Wno-sizeof-pointer-memaccess)
endif()

target_include_directories(${PROJECT_NAME} 
    PUBLIC  
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/src>  
    $<INSTALL_INTERFACE:include/${PROJECT_NAME}>  # <prefix>/include/${PROJECT_NAME}
    PRIVATE
    src
)

include(GNUInstallDirs)

install(TARGETS ${PROJECT_NAME}
    EXPORT ${PROJECT_NAME}Config
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}  # This is for Windows
    ARCHIVE DESTINATION ${CMAKE_INSTALL_BINDIR}
    PUBLIC_HEADER DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/${PROJECT_NAME})

# Make Library "find_project-able" by cmake, if installed.
install(EXPORT ${PROJECT_NAME}Config DESTINATION share/${PROJECT_NAME}Config/cmake)

export(TARGETS ${PROJECT_NAME} FILE "${PROJECT_NAME}Config.cmake")
