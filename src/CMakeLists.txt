CMAKE_MINIMUM_REQUIRED(VERSION 3.14 FATAL_ERROR)
set (CMAKE_CXX_STANDARD 17)

# CMake 3.14+
include(FetchContent)

FetchContent_Declare(
  relic
  GIT_REPOSITORY https://github.com/relic-toolkit/relic.git
  GIT_TAG        origin/main
)
FetchContent_MakeAvailable(relic)

file(GLOB HEADERS ${CMAKE_CURRENT_SOURCE_DIR}/*.hpp)
source_group("SrcHeaders" FILES ${HEADERS})

include_directories(
  ${INCLUDE_DIRECTORIES}
  ${relic_SOURCE_DIR}/include
  ${relic_BINARY_DIR}/include
  ${CMAKE_CURRENT_SOURCE_DIR}/../contrib/catch
  ${GMP_INCLUDES}
  )

add_library(bls ${HEADERS}
  ${CMAKE_CURRENT_SOURCE_DIR}/privatekey.cpp
  ${CMAKE_CURRENT_SOURCE_DIR}/bls.cpp
  ${CMAKE_CURRENT_SOURCE_DIR}/elements.cpp
  ${CMAKE_CURRENT_SOURCE_DIR}/schemes.cpp
)
if (SODIUM_FOUND)
target_compile_definitions(bls PRIVATE BLSALLOC_SODIUM=1)
endif()

target_link_libraries(bls relic_s)
if (SODIUM_FOUND)
  target_link_libraries(bls sodium)
endif()


add_executable(runtest test.cpp)
add_executable(runbench test-bench.cpp)

if (SODIUM_FOUND)
  target_link_libraries(runtest bls)
  target_link_libraries(runbench bls)
else()
  target_link_libraries(runtest bls)
  target_link_libraries(runbench bls)
endif()
