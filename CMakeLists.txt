cmake_minimum_required(VERSION 3.24.1)

project(midi)

add_library(
  ${PROJECT_NAME}
  SHARED
  src/input.cpp
  src/output.cpp
  src/midi.cpp
  ${CMAKE_JS_SRC}
)
include(cmake/CPM.cmake)

cpmaddpackage ("gh:justinlatimer/rtmidi#c01a721")

# Gives our library file a .node extension without any "lib" prefix
set_target_properties(${PROJECT_NAME} PROPERTIES PREFIX "" SUFFIX ".node")

#target_include_directories(${PROJECT_NAME} PRIVATE "${CMAKE_SOURCE_DIR}/node_modules/node-addon-api")

#target_include_directories(${PROJECT_NAME} PRIVATE vendor/rtmidi)
# Essential include files to build a node addon,
# You should add this line in every CMake.js based project
target_include_directories(${PROJECT_NAME} PRIVATE ${CMAKE_JS_INC})
target_compile_features(${PROJECT_NAME} PRIVATE cxx_std_17)

#add_subdirectory(vendor/rtmidi)
# Essential library files to link to a node addon
# You should add this line in every CMake.js based project

target_link_libraries(
  ${PROJECT_NAME}
  rtmidi
  ${CMAKE_JS_LIB}
)
if(MSVC AND CMAKE_JS_NODELIB_DEF AND CMAKE_JS_NODELIB_TARGET)
  # Generate node.lib
  execute_process(COMMAND ${CMAKE_AR} /def:${CMAKE_JS_NODELIB_DEF} /out:${CMAKE_JS_NODELIB_TARGET} ${CMAKE_STATIC_LINKER_FLAGS})
endif()
