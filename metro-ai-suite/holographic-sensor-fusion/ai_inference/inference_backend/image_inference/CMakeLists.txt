# ==============================================================================
# Copyright (C) 2018-2024 Intel Corporation
#
# SPDX-License-Identifier: MIT
# ==============================================================================

set (TARGET_NAME "image_inference")

add_subdirectory(openvino)
find_package(PkgConfig REQUIRED)
pkg_check_modules(GSTREAMER gstreamer-1.0>=1.16 REQUIRED)
pkg_check_modules(GSTVIDEO gstreamer-video-1.0>=1.16 REQUIRED)

if(${ENABLE_VAAPI})
        add_subdirectory(async_with_va_api)
endif()

find_package(PkgConfig REQUIRED)
find_package(OpenVINO REQUIRED Runtime)

file (GLOB MAIN_SRC
        ${CMAKE_CURRENT_SOURCE_DIR}/*.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/*.c
        )

file (GLOB MAIN_HEADERS
        ${CMAKE_CURRENT_SOURCE_DIR}/*.h
        )

add_library(${TARGET_NAME} STATIC ${MAIN_SRC} ${MAIN_HEADERS})
set_compile_flags(${TARGET_NAME})

target_include_directories(${TARGET_NAME}
PUBLIC
        ${CMAKE_CURRENT_SOURCE_DIR}
        ${CMAKE_CURRENT_SOURCE_DIR}/openvino
        ${CMAKE_CURRENT_SOURCE_DIR}/async_with_va_api
        ${GSTREAMER_INCLUDE_DIRS}
)

target_link_libraries(${TARGET_NAME}
PUBLIC
        openvino::runtime
        logger
        utils
        ${GSTREAMER_LIBRARIES}
)
