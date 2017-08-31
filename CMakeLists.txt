# stop if cmake version below 2.8
cmake_minimum_required(VERSION 2.8 FATAL_ERROR)

# project name
project(minizip)

# enable fortran, c, and c++ language
enable_language(C CXX)

# enable c++11
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")

# detect operating system
message(STATUS "We are on a ${CMAKE_SYSTEM_NAME} system")
if(${CMAKE_SYSTEM_NAME} STREQUAL "Linux")
    add_definitions(-DSYSTEM_LINUX)
endif()
if(${CMAKE_SYSTEM_NAME} STREQUAL "Darwin")
    add_definitions(-DSYSTEM_DARWIN)
endif()
if(${CMAKE_SYSTEM_NAME} STREQUAL "AIX")
    add_definitions(-DSYSTEM_AIX)
endif()
if(${CMAKE_SYSTEM_NAME} MATCHES "Windows")
    add_definitions(-DSYSTEM_WINDOWS)
endif()

set(TARGET_SRC src/ioapi.c src/miniunz.c src/mztools.c src/unzip.c src/zip.c)

if(${CMAKE_SYSTEM_NAME} MATCHES "Windows")
    set(TARGET_SRC src/iowin32.c src/ioapi.c src/miniunz.c src/mztools.c src/unzip.c src/zip.c)
endif()

set(TARGET_HDR include)
include_directories(${TARGET_HDR} include)

add_library(minizip SHARED ${TARGET_SRC})
target_include_directories(minizip PUBLIC
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
  $<INSTALL_INTERFACE:include>  # <prefix>/include
)

add_executable(test-unzip test/main.cpp ${TARGET_SRC})
target_include_directories(test-unzip PUBLIC ${TARGET_HDR})
TARGET_LINK_LIBRARIES(test-unzip z)


add_executable(test-zip src/minizip.c ${TARGET_SRC})
target_include_directories(test-zip PUBLIC ${TARGET_HDR})
TARGET_LINK_LIBRARIES(test-zip z)
#----------------------------------------------------------------------------
#----------------------------------------------------------------------------
# Install instructions.
#----------------------------------------------------------------------------
install(
  TARGETS minizip
  RUNTIME DESTINATION bin
  LIBRARY DESTINATION lib
)

install(
  TARGETS test-zip
  RUNTIME DESTINATION bin
  LIBRARY DESTINATION lib
)

install(
  TARGETS test-unzip
  RUNTIME DESTINATION bin
  LIBRARY DESTINATION lib
)

install (
    DIRECTORY ${CMAKE_SOURCE_DIR}/include/
    DESTINATION include/minizip
    FILES_MATCHING PATTERN "*.h*")

