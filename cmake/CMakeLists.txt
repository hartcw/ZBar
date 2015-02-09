cmake_minimum_required (VERSION 2.8.11)
project("zbar")
include(GenerateExportHeader)

set(ZBAR_VERSION_MAJOR 0)
set(ZBAR_VERSION_MINOR 2)
set(ZBAR_VERSION_REVISION 0)

option(ENABLE_EAN "Enable EAN" ON)
option(ENABLE_DATABAR "Enable Databar" ON)
option(ENABLE_CODE128 "Enable Code128" ON)
option(ENABLE_CODE93 "Enable Code93" ON)
option(ENABLE_CODE39 "Enable Code39" ON)
option(ENABLE_CODABAR "Enable Codabar" ON)
option(ENABLE_I25 "Enable I25" ON)
option(ENABLE_PDF417 "Enable PDF417" OFF)
option(ENABLE_QRCODE "Enable QRCode" ON)
option(HAVE_GRAPHICSMAGICK "Use GraphicsMagick" OFF)
option(HAVE_IMAGEMAGICK "Use ImageMagick" OFF)

set(ZBAR_SOURCES
    ../zbar/config.c
    ../zbar/error.c
    ../zbar/symbol.c
    ../zbar/image.c
    ../zbar/convert.c
    ../zbar/processor.c
    ../zbar/processor/lock.c
    ../zbar/refcnt.c
    ../zbar/window.c
    ../zbar/video.c
    ../zbar/img_scanner.c
    ../zbar/scanner.c
    ../zbar/decoder.c
    ../zbar/video/null.c)

if(ENABLE_EAN)
LIST(APPEND ZBAR_SOURCES ../zbar/decoder/ean.c)
endif()

if(ENABLE_DATABAR)
LIST(APPEND ZBAR_SOURCES ../zbar/decoder/databar.c)
endif()

if(ENABLE_CODE128)
LIST(APPEND ZBAR_SOURCES ../zbar/decoder/code128.c)
endif()

if(ENABLE_CODE93)
LIST(APPEND ZBAR_SOURCES ../zbar/decoder/code93.c)
endif()

if(ENABLE_CODE39)
LIST(APPEND ZBAR_SOURCES ../zbar/decoder/code39.c)
endif()

if(ENABLE_CODABAR)
LIST(APPEND ZBAR_SOURCES ../zbar/decoder/codabar.c)
endif()

if(ENABLE_I25)
LIST(APPEND ZBAR_SOURCES ../zbar/decoder/i25.c)
endif()

if(ENABLE_PDF417)
LIST(APPEND ZBAR_SOURCES ../zbar/decoder/pdf417.c)
endif()

if(ENABLE_QRCODE)
LIST(APPEND ZBAR_SOURCES
    ../zbar/decoder/qr_finder.c
    ../zbar/qrcode/qrdec.c
    ../zbar/qrcode/qrdectxt.c
    ../zbar/qrcode/rs.c
    ../zbar/qrcode/isaac.c
    ../zbar/qrcode/bch15_5.c
    ../zbar/qrcode/binarize.c
    ../zbar/qrcode/util.c)
endif()

if (WIN32)
LIST(APPEND ZBAR_SOURCES
    ../zbar/processor/win.c
    ../zbar/window/win.c
    ../zbar/window/dib.c
    ../zbar/libzbar.rc)
else()
LIST(APPEND ZBAR_SOURCES
    ../zbar/processor/posix.c
    ../zbar/processor/null.c
    ../zbar/window/null.c)
endif()

if(MSVC)
set(CMAKE_C_FLAGS "/wd4244 /wd4267")
endif()

include_directories("${PROJECT_BINARY_DIR}")
include_directories(../include)
include_directories(../zbar)
include_directories("${CMAKE_INSTALL_PREFIX}/include")
link_directories("${CMAKE_INSTALL_PREFIX}/lib")

configure_file(config.h.in "${PROJECT_BINARY_DIR}/config.h")

add_library(zbar SHARED ${ZBAR_SOURCES})
target_link_libraries(zbar winmm.lib iconv.lib)

GENERATE_EXPORT_HEADER(zbar
    BASE_NAME ZBAR
    EXPORT_MACRO_NAME ZBAR_EXPORT
    EXPORT_FILE_NAME zbar_export.h
    STATIC_DEFINE ZBAR_BUILT_AS_STATIC)

install(FILES "${PROJECT_BINARY_DIR}/zbar_export.h"
    DESTINATION include)
install(DIRECTORY ../include/
    DESTINATION include
    FILES_MATCHING PATTERN "*.h")
install(TARGETS zbar
    RUNTIME DESTINATION bin
    LIBRARY DESTINATION lib
    ARCHIVE DESTINATION lib)
