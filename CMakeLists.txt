cmake_minimum_required(VERSION 2.8)
project(DRMClearKey)

set(CMAKE_MODULE_PATH "${CMAKE_SOURCE_DIR}/cmake")

set(DRM_PLUGIN_NAME "ClearKey")

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")

option(OPTEE_AES_DECRYPTOR "Enable AES Decryption on OP-TEE environment" OFF)

if(NOT NAMESPACE)
   set(NAMESPACE "WPEFramework")
endif()

if(OPTEE_AES_DECRYPTOR)
find_package(OPTEEClearKey REQUIRED)
add_compile_definitions(OPTEE_AES128)
else()
find_package(ClearKey REQUIRED)
endif()

file(GLOB DRM_PLUGIN_INCLUDES *.h)

set(DRM_PLUGIN_LIBS 
    ${CLEARKEY_LIBRARIES})

set(DRM_PLUGIN_SOURCES 
    MediaSession.cpp 
    MediaSystem.cpp
    JSONWebKey.cpp
    jsmn/jsmn.c
    )

# add the library
add_library(${DRM_PLUGIN_NAME} SHARED ${DRM_PLUGIN_SOURCES})
target_include_directories(${DRM_PLUGIN_NAME}
    PRIVATE
    ${CMAKE_SYSROOT}/usr/include/${NAMESPACE}
    ${CEARKEY_INCLUDE_DIRS} "./jsmn")
target_link_libraries(${DRM_PLUGIN_NAME} ${DRM_PLUGIN_LIBS})
set_target_properties(${DRM_PLUGIN_NAME} PROPERTIES SUFFIX ".drm")
set_target_properties(${DRM_PLUGIN_NAME} PROPERTIES PREFIX "")

install(TARGETS ${DRM_PLUGIN_NAME} DESTINATION ${CMAKE_INSTALL_PREFIX}/share/${NAMESPACE}/OCDM)

