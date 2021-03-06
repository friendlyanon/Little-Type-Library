cmake_minimum_required(VERSION 3.14)

project(LittleTypeLibraryTests LANGUAGES CXX)

# ---- Add root project ----

# We want warnings in tests
set(LittleTypeLibrary_INCLUDE_WITHOUT_SYSTEM ON CACHE INTERNAL "")

include(FetchContent)
FetchContent_Declare(LittleTypeLibrary SOURCE_DIR "${CMAKE_CURRENT_LIST_DIR}/..")
FetchContent_MakeAvailable(LittleTypeLibrary)

# ---- Add googletest ----

FetchContent_Declare(googletest
        GIT_REPOSITORY "https://github.com/google/googletest.git"
        GIT_TAG aee0f9d9b5b87796ee8a0ab26b7587ec30e8858e
        UPDATE_DISCONNECTED YES)
FetchContent_MakeAvailable(googletest)

# ---- Tests ----

enable_testing()

# ---- Retrieve files ----
file(GLOB_RECURSE FileList "${CMAKE_CURRENT_LIST_DIR}/../include/*.h")
message(${CMAKE_CURRENT_LIST_DIR})
add_executable(LTLtest source/test.cpp ${FileList})

target_link_libraries(LTLtest PRIVATE
        LittleTypeLibrary::LTL
        gtest_main)

target_compile_features(LTLtest PRIVATE cxx_std_17)

if(CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  target_compile_options(LTLtest PRIVATE -ftime-trace)
elseif(MSVC)
  if(CMAKE_BUILD_TYPE STREQUAL "Debug")
    target_compile_options(LTLtest PRIVATE /MTd /Zi /DEBUG:FULL)
  else()
    target_compile_options(LTLtest PRIVATE /MT)
  endif()

  target_compile_options(LTLtest PRIVATE /permissive- /d2cgsummary /Wv:18 /bigobj)
endif()

add_test(NAME LTLtest COMMAND LTLtest)
