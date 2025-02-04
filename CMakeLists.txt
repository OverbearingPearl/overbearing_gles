cmake_minimum_required(VERSION 3.16)
project(overbearing_gles)

set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
set(CMAKE_INCLUDE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/include)

find_program(MAKE_PATH make)
if(MAKE_PATH)
  message(STATUS "Found make: ${MAKE_PATH}")
else()
  message(FATAL_ERROR "Make was not installed.")
endif()

message(STATUS "Building GLEW using Makefile...")
execute_process(
  COMMAND ${MAKE_PATH} -C ${CMAKE_SOURCE_DIR}/third_party/glew/auto
  WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}/third_party/glew/auto
  RESULT_VARIABLE GLEW_BUILD_RESULT
)
if(NOT GLEW_BUILD_RESULT EQUAL 0)
  message(FATAL_ERROR "Failed to build GLEW. Make returned error code: ${GLEW_BUILD_RESULT}")
else()
  message(STATUS "Successfully built GLEW.")
endif()

add_subdirectory(${CMAKE_SOURCE_DIR}/third_party/glew/build/cmake)
set_target_properties(glew PROPERTIES
  LIBRARY_OUTPUT_DIRECTORY "${CMAKE_LIBRARY_OUTPUT_DIRECTORY}/glew"
  ARCHIVE_OUTPUT_DIRECTORY "${CMAKE_ARCHIVE_OUTPUT_DIRECTORY}/glew"
  INCLUDE_OUTPUT_DIRECTORY "${CMAKE_INCLUDE_OUTPUT_DIRECTORY}/glew"
)

add_subdirectory(${CMAKE_SOURCE_DIR}/third_party/glfw)
set_target_properties(glfw PROPERTIES
  LIBRARY_OUTPUT_DIRECTORY "${CMAKE_LIBRARY_OUTPUT_DIRECTORY}/glfw"
  ARCHIVE_OUTPUT_DIRECTORY "${CMAKE_ARCHIVE_OUTPUT_DIRECTORY}/glfw"
  INCLUDE_OUTPUT_DIRECTORY "${CMAKE_INCLUDE_OUTPUT_DIRECTORY}/glfw"
)

add_executable(${PROJECT_NAME} src/main.cc)
target_link_libraries(${PROJECT_NAME} PRIVATE glew glfw)
