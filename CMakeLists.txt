cmake_minimum_required(VERSION 3.9)
project(Live555 C CXX)

file(GLOB HEADERS
    "${SOURCE_PATH}/BasicUsageEnvironment/include/*.h*"
    "${SOURCE_PATH}/groupsock/include/*.h*"
    "${SOURCE_PATH}/liveMedia/include/*.h*"
    "${SOURCE_PATH}/UsageEnvironment/include/*.h*"
)

include_directories(
    BasicUsageEnvironment/include
    groupsock/include
    liveMedia/include
    UsageEnvironment/include
)

if (NOT MSVC)
    add_compile_options(-DSOCKLEN_T=socklen_t)
endif()

file(GLOB BASIC_USAGE_ENVIRONMENT_SRCS BasicUsageEnvironment/*.c BasicUsageEnvironment/*.cpp)
add_library(BasicUsageEnvironment ${BASIC_USAGE_ENVIRONMENT_SRCS})
target_include_directories(BasicUsageEnvironment 
                           PUBLIC 
                           $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/BasicUsageEnvironment/include>
                           $<INSTALL_INTERFACE:include/Live555/BasicUsageEnvironment>)

file(GLOB GROUPSOCK_SRCS groupsock/*.c groupsock/*.cpp)
add_library(groupsock ${GROUPSOCK_SRCS})
target_include_directories(groupsock 
                           PUBLIC 
                           $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/groupsock/include>
                           $<INSTALL_INTERFACE:include/Live555/groupsock>)

file(GLOB LIVEMEDIA_SRCS liveMedia/*.c liveMedia/*.cpp)
add_library(liveMedia ${LIVEMEDIA_SRCS})
find_package(OpenSSL REQUIRED)
target_include_directories(liveMedia 
                           PUBLIC 
                           $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/liveMedia/include>
                           $<INSTALL_INTERFACE:include/Live555/liveMedia>)
target_link_libraries(liveMedia PUBLIC OpenSSL::SSL OpenSSL::Crypto)

file(GLOB USAGE_ENVIRONMENT_SRCS UsageEnvironment/*.c UsageEnvironment/*.cpp)
add_library(UsageEnvironment ${USAGE_ENVIRONMENT_SRCS})
target_include_directories(UsageEnvironment 
                           PUBLIC 
                           $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/UsageEnvironment/include>
                           $<INSTALL_INTERFACE:include/Live555/UsageEnvironment>)

install(TARGETS groupsock BasicUsageEnvironment liveMedia UsageEnvironment
        EXPORT ${PROJECT_NAME}Targets
        RUNTIME DESTINATION bin
        LIBRARY DESTINATION lib
        ARCHIVE DESTINATION lib
)

install(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/BasicUsageEnvironment/include/ DESTINATION include/Live555/BasicUsageEnvironment)
install(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/liveMedia/include/ DESTINATION include/Live555/liveMedia)
install(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/UsageEnvironment/include/ DESTINATION include/Live555/UsageEnvironment)
install(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/groupsock/include/ DESTINATION include/Live555/groupsock)

install(EXPORT ${PROJECT_NAME}Targets
        FILE ${PROJECT_NAME}Targets.cmake
        NAMESPACE ${PROJECT_NAME}::
        DESTINATION cmake/${PROJECT_NAME})

install(FILES
        ${PROJECT_NAME}Config.cmake
        DESTINATION cmake/${PROJECT_NAME})