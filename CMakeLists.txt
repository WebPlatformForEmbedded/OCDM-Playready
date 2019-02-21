cmake_minimum_required(VERSION 3.3)

find_package(WPEFramework)

list(APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/cmake")

if(NOT NAMESPACE)
   set(NAMESPACE "WPEFramework")
endif()

set(PLUGIN_NAME DRMPlayready)
set(MODULE_NAME ${NAMESPACE}${PLUGIN_NAME})

find_package(${NAMESPACE}Plugins REQUIRED)
find_package(PlayReady REQUIRED)

add_library(${MODULE_NAME} SHARED
    MediaSession.cpp
    MediaSystem.cpp
    MediaSessionExt.cpp
    MediaSystemExt.cpp)

set_target_properties(${MODULE_NAME} PROPERTIES
        CXX_STANDARD 11
        CXX_STANDARD_REQUIRED YES)


target_include_directories(${MODULE_NAME}
    PUBLIC
    "${STAGING_DIR}/usr/include"
    "${STAGING_DIR}/usr/include/WPEFramework"
    "${STAGING_DIR}/usr/include/playready"
    ${PLAYREADY_INCLUDE}
    "${PLAYREADY_INCLUDE}/oem/inc"
    "${PLAYREADY_INCLUDE}/oem/ansi/inc"
    "${PLAYREADY_INCLUDE}/oem/common/inc"
    "${PLAYREADY_INCLUDE}/playready.cmake")


target_compile_definitions(${MODULE_NAME}
        PRIVATE
            PLAYREADY=ON
            PLAYREADY2=ON
            PLAYREADY2TEE=ON
            PLAYREADY2_5=ON
            PLAYREADY2_5_TEE=ON
            PLAYREADY_5_TEE_LDL=ON)

target_link_libraries(${MODULE_NAME}
    PRIVATE
        ${NAMESPACE}Plugins::${NAMESPACE}Plugins
        PlayReady::PlayReady)


# Library installation section
string(TOLOWER ${NAMESPACE} STORAGENAME)
install(TARGETS ${MODULE_NAME} DESTINATION ${CMAKE_INSTALL_PREFIX}/lib/${STORAGENAME}/plugins)

write_config(${PLUGIN_NAME})
