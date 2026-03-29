cmake_minimum_required(VERSION 3.16)
project(webview-builder LANGUAGES C CXX)

if(NOT CMAKE_BUILD_TYPE)
  message(STATUS "No CMAKE_BUILD_TYPE specified, defaulting to Debug")
  set(CMAKE_BUILD_TYPE Debug CACHE STRING "Build type" FORCE)
endif()

if(MSVC AND CMAKE_CXX_COMPILE_FEATURES STREQUAL "")
  # CMake 4.x can fail to populate MSVC compile features in some environments,
  # which breaks upstream webview's target_compile_features(cxx_std_11) checks.
  set(CMAKE_CXX_COMPILE_FEATURES
      cxx_std_98
      cxx_std_11
      cxx_std_14
      cxx_std_17
      cxx_std_20
      cxx_std_23
      cxx_std_26)
  set(CMAKE_CXX98_COMPILE_FEATURES cxx_std_98)
  set(CMAKE_CXX11_COMPILE_FEATURES cxx_std_11)
  set(CMAKE_CXX14_COMPILE_FEATURES cxx_std_14)
  set(CMAKE_CXX17_COMPILE_FEATURES cxx_std_17)
  set(CMAKE_CXX20_COMPILE_FEATURES cxx_std_20)
  set(CMAKE_CXX23_COMPILE_FEATURES cxx_std_23)
  set(CMAKE_CXX26_COMPILE_FEATURES cxx_std_26)
endif()

set(CMAKE_RUNTIME_OUTPUT_DIRECTORY "${CMAKE_SOURCE_DIR}/bin")
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY "${CMAKE_SOURCE_DIR}/lib")
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY "${CMAKE_SOURCE_DIR}/lib")

include(FetchContent)

set(WEBVIEW_GIT_TAG
    "cbbdee44afff22867de9fd88a9fc8350d9bdd399"
    CACHE STRING "Pinned upstream webview revision")

set(WEBVIEW_BUILD_TESTS             OFF     CACHE BOOL   "" FORCE)
set(WEBVIEW_BUILD_EXAMPLES          OFF     CACHE BOOL   "" FORCE)
set(WEBVIEW_USE_CLANG_TOOLS         OFF     CACHE BOOL   "" FORCE)
set(WEBVIEW_ENABLE_CHECKS           OFF     CACHE BOOL   "" FORCE)
set(WEBVIEW_USE_CLANG_TIDY          OFF     CACHE BOOL   "" FORCE)
set(WEBVIEW_BUILD_DOCS              OFF     CACHE BOOL   "" FORCE)
set(WEBVIEW_USE_CLANG_FORMAT        OFF     CACHE BOOL   "" FORCE)
set(WEBVIEW_BUILD_SHARED_LIBRARY    OFF     CACHE BOOL   "" FORCE)
set(WEBVIEW_BUILD_STATIC_LIBRARY    ON      CACHE BOOL   "" FORCE)
if(WIN32)
  # MoonBit native objects on Windows are built against the static CRT
  # (`LIBCMT`), so align the bundled webview library to avoid linker noise.
  set(CMAKE_MSVC_RUNTIME_LIBRARY "MultiThreaded")
  set(WEBVIEW_USE_BUILTIN_MSWEBVIEW2 ON     CACHE BOOL   "" FORCE)
endif()
set(WEBVIEW_PACKAGE_HEADERS         ON      CACHE BOOL   "" FORCE)
set(WEBVIEW_INSTALL_TARGETS         ON      CACHE BOOL   "" FORCE)

FetchContent_Declare(
    webview
    GIT_REPOSITORY https://github.com/webview/webview
    GIT_TAG "${WEBVIEW_GIT_TAG}"
    GIT_SHALLOW TRUE)

FetchContent_MakeAvailable(webview)

if(WIN32 AND TARGET webview_core_static)
  # We only build the static target on Windows, so reuse the canonical file
  # name expected by MoonBit link flags and existing templates.
  set_target_properties(webview_core_static PROPERTIES OUTPUT_NAME webview)
endif()
