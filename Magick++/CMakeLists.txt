cmake_minimum_required(VERSION 3.0.0)
project(Magick++ VERSION 0.1.0 LANGUAGES C CXX)

find_package(ZLIB REQUIRED)

file(GLOB Magick_SOURCES "${CMAKE_CURRENT_SOURCE_DIR}/../magick/*.c")
add_library(magick STATIC
  ${Magick_SOURCES}
  ${CMAKE_CURRENT_SOURCE_DIR}/../magick/magick_config.h #make it available in VS solution view
  ${CMAKE_CURRENT_SOURCE_DIR}/../magick/common.h
  )
if(WIN32)#True on windows systems, including win64
  target_compile_definitions(magick PUBLIC WIN64)
  target_compile_definitions(magick PRIVATE _LIB) #build magick lib as c lib
  target_compile_definitions(magick PUBLIC _CRT_SECURE_NO_WARNINGS)
endif()
target_include_directories(magick PRIVATE 
  "${CMAKE_CURRENT_SOURCE_DIR}/../"
  ${ZLIB_INCLUDE_DIRS}
  )  
target_link_libraries(magick PRIVATE
  ${ZLIB_LIBRARIES}
)  


file(GLOB SOURCES "lib/*.cpp")
add_library(Magick++ SHARED
  ${SOURCES}
  "lib/Magick++/Include.h"
  )
if(WIN32)#True on windows systems, including win64
  target_compile_definitions(Magick++ PUBLIC WIN64)
  target_compile_definitions(Magick++ PRIVATE _LIB) #to use magick lib as c lib
endif()  
# target_compile_definitions(Magick++ PRIVATE _LIB) # To use core/wand/coders header
# target_compile_definitions(Magick++ PRIVATE MAGICPP_DLL) # For Magic++  header
target_include_directories(Magick++ PRIVATE
  ${CMAKE_CURRENT_SOURCE_DIR}/../
)
target_include_directories(Magick++ PUBLIC #public headers
  "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/lib>"
  "$<INSTALL_INTERFACE:include>"
)
target_link_libraries(Magick++ PRIVATE magick)


install(TARGETS Magick++
  EXPORT Magick++Targets
  ARCHIVE DESTINATION lib
  LIBRARY DESTINATION lib
  RUNTIME DESTINATION bin
)
install(DIRECTORY lib/Magick++  DESTINATION include)
install(FILES lib/Magick++.h DESTINATION include/Magick++)

file(GLOB Magick_HEADERS "${CMAKE_CURRENT_SOURCE_DIR}/../magick/*.h")
file(GLOB Magick_PRIVATE_HEADERS "${CMAKE_CURRENT_SOURCE_DIR}/../magick/*-private.h")
list(REMOVE_ITEM Magick_HEADERS ${Magick_PRIVATE_HEADERS})
install(
  FILES ${Magick_HEADERS} DESTINATION include/magick
  )


configure_file(Magick++Config.cmake.in Magick++Config.cmake @ONLY)
install(FILES "${CMAKE_CURRENT_BINARY_DIR}/Magick++Config.cmake"
    DESTINATION lib/cmake/Magick++
)
install(EXPORT Magick++Targets
        FILE Magick++Targets.cmake
        NAMESPACE Magick++::
        DESTINATION lib/cmake/Magick++)

include(CTest)
enable_testing()

set(CPACK_PROJECT_NAME ${PROJECT_NAME})
set(CPACK_PROJECT_VERSION ${PROJECT_VERSION})
include(CPack)
