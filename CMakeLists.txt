cmake_minimum_required(VERSION 3.10.2)
project(echobot)

set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall")
set(Boost_USE_MULTITHREADED ON)

find_package(Threads REQUIRED)
find_package(OpenSSL REQUIRED)
find_package(Boost COMPONENTS system REQUIRED)
find_package(CURL)

include_directories(/usr/local/include ${OPENSSL_INCLUDE_DIR} ${Boost_INCLUDE_DIR})

if (CURL_FOUND)
    include_directories(${CURL_INCLUDE_DIRS})
    add_definitions(-DHAVE_CURL)
endif()

find_library(TGBOT_LIB TgBot /usr/local/lib64 /usr/local/lib)

if (NOT TGBOT_LIB)
    message(STATUS "TgBot library not found in /usr/local/lib64. Looking in /usr/local/lib.")
    find_library(TGBOT_LIB TgBot /usr/local/lib)
endif()

if (NOT TGBOT_LIB)
    message(FATAL_ERROR "TgBot library not found.")
endif()

add_executable(echobot src/main.cpp)

target_link_libraries(echobot ${TGBOT_LIB} ${CMAKE_THREAD_LIBS_INIT} ${OPENSSL_LIBRARIES} ${Boost_LIBRARIES} ${CURL_LIBRARIES})
