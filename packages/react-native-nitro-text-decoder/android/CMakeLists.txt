project(NitroTextDecoder)
cmake_minimum_required(VERSION 3.9.0)

set (PACKAGE_NAME NitroTextDecoder)
set (CMAKE_VERBOSE_MAKEFILE ON)
set (CMAKE_CXX_STANDARD 20)

# Define C++ library and add all sources
add_library(${PACKAGE_NAME} SHARED
        src/main/cpp/cpp-adapter.cpp
        ../cpp/HybridTextEncoding.cpp
        ../cpp/HybridTextDecoder.cpp
        ../cpp/TextDecoderUtils.cpp
        ../cpp/simdutf.cpp
)

# Enable simdutf NEON UTF-8->UTF-16 transcoder in Tier B of decodeRaw.
# Drop base64 since the decoder only needs UTF support (saves ~20-30 KB).
target_compile_definitions(${PACKAGE_NAME} PRIVATE
        NITRO_TEXTDECODER_USE_SIMDUTF
        SIMDUTF_FEATURE_BASE64=0
)

# Add Nitrogen specs :)
include(${CMAKE_SOURCE_DIR}/../nitrogen/generated/android/NitroTextDecoder+autolinking.cmake)

# Set up local includes
include_directories(
        "src/main/cpp"
        "../cpp"
)

find_library(LOG_LIB log)

# Link all libraries together
target_link_libraries(
        ${PACKAGE_NAME}
        ${LOG_LIB}
        android                                   # <-- Android core
)
