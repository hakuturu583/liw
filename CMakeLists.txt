cmake_minimum_required(VERSION 2.8.12)
project(liw)
find_package(zeda REQUIRED)
add_definitions("-Wall -g -O3")

set(CMAKE_C_COMPILER "gcc")

include_directories(include)

# Build
set(${PROJECT_NAME}_SRC
    src/liw_time.c 
    src/liw_coproc.c
    src/liw_paction.c
    src/liw_fifo.c
    src/liw_ipc.c
    src/liw_lock.c
	src/liw_socket.c
    )

option(USE_LIBDL "USE_LIBDL" ON)
if(USE_LIBDL)
    set(${PROJECT_NAME}_SRC
        ${${PROJECT_NAME}_SRC}
        src/liw_dl.c
    )
endif()

option(USE_RTC "USE_RTC" ON)
if(USE_RTC)
    set(${PROJECT_NAME}_SRC
        ${${PROJECT_NAME}_SRC}
        src/liw_rtc.c
    )
endif()

add_library(${PROJECT_NAME} SHARED
    ${${PROJECT_NAME}_SRC})
target_link_libraries(${PROJECT_NAME} zeda)

# Install
INSTALL (
    DIRECTORY ${CMAKE_SOURCE_DIR}/include/
    DESTINATION include
    FILES_MATCHING PATTERN "*.h*")

install(TARGETS ${PROJECT_NAME} LIBRARY DESTINATION lib)

install(TARGETS ${PROJECT_NAME}
    EXPORT ${PROJECT_NAME}-export
    LIBRARY        DESTINATION lib
    INCLUDES       DESTINATION include
    PUBLIC_HEADER  DESTINATION include/${PROJECT_NAME})

install(EXPORT ${PROJECT_NAME}-export
    FILE ${PROJECT_NAME}-config.cmake
    DESTINATION share/cmake/${PROJECT_NAME}
    EXPORT_LINK_INTERFACE_LIBRARIES)