cmake_minimum_required(VERSION 3.15)

project(ax25beacon VERSION 0.9)

set(TARGET_LIB_NAME ${PROJECT_NAME})


add_library(${TARGET_LIB_NAME}
  src/ax25.c
  src/ax25beacon.c)

target_include_directories(${TARGET_LIB_NAME} PUBLIC include)

set_target_properties(${TARGET_LIB_NAME}
    PROPERTIES
    ARCHIVE_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/lib"
)

set(CMAKE_BUILD_TYPE Release)
set(COMPILE_OPTIONS "-Wall")
set(COMPILE_RELEASE_OPTIONS "${COMPILE_OPTIONS};-O3")
target_compile_options(${TARGET_LIB_NAME} PRIVATE "$<$<CONFIG:Release>:${COMPILE_RELEASE_OPTIONS}>")

# We won't build the example on bare-metal systems as file I/O is required
if (CMAKE_SYSTEM_VERSION) # Check if the target system has an OS
  set(TARGET_EXAMPLE_EXE_NAME ${PROJECT_NAME}Example)

  include(FetchContent)
  FetchContent_Declare(libwav
      GIT_REPOSITORY    "https://github.com/eleccoder/libwav.git" 
      GIT_SHALLOW       ON
      )

  FetchContent_MakeAvailable(libwav)

  add_executable(${TARGET_EXAMPLE_EXE_NAME} src/example.c)
  target_include_directories(${TARGET_EXAMPLE_EXE_NAME} PRIVATE include)
  target_link_libraries(${TARGET_EXAMPLE_EXE_NAME} ${PROJECT_NAME} wav::wav m)
endif()
