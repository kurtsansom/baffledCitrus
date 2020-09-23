# Minimum required version of CMake
cmake_minimum_required(VERSION 3.14)
# if(COMMAND CMAKE_POLICY)
#   CMAKE_POLICY(SET CMP0003 NEW)
#   if ("${CMAKE_VERSION}" VERSION_GREATER 2.99)
#     CMAKE_POLICY(SET CMP0026 OLD)
#   endif ("${CMAKE_VERSION}" VERSION_GREATER 2.99)
# endif(COMMAND CMAKE_POLICY)

# Set CMake project name
project(LEMON
  VERSION 3.33.1
  LANGUAGES C
)

set(CMAKE_C_STANDARD 11)
set(CMAKE_C_STANDARD_REQUIRED on)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

# Set CMake Install directories for LEMON
set(LEMON_INSTALL_BIN_DIR bin CACHE PATH "Installation path for LEMON binary files")

# configure optimization
if (CMAKE_BUILD_TYPE STREQUAL "Debug")
    set(OPTIMIZATION_FLAGS "-O0 -DDEBUG")
    message("-- Configuring debug build")
else()
    set(OPTIMIZATION_FLAGS "-O3 -DNDEBUG")
    message("-- Configuring release build")
endif()

add_executable(lemon lemon.c)

set_target_properties(lemon
   PROPERTIES
   C_STANDARD "${CMAKE_C_STANDARD}"
   C_STANDARD_REQUIRED "YES"
   C_EXTENSION "NO"
)

# Handle lempar.c template file
# message("test ${CMAKE_CONFIGURATION_TYPES} ")
# if(NOT CMAKE_CONFIGURATION_TYPES)
set(LEMPAR_DIR ${CMAKE_BINARY_DIR})
# else(NOT CMAKE_CONFIGURATION_TYPES)
#   set(LEMPAR_DIR ${CMAKE_BINARY_DIR}/${CMAKE_CFG_INTDIR}/${BIN_DIR})
# endif(NOT CMAKE_CONFIGURATION_TYPES)
add_custom_command(
  OUTPUT ${LEMPAR_DIR}/lempar.c
  COMMAND ${CMAKE_COMMAND} -E copy ${CMAKE_CURRENT_SOURCE_DIR}/lempar.c ${LEMPAR_DIR}/lempar.c
  DEPENDS ${CMAKE_CURRENT_SOURCE_DIR}/lempar.c
  )
add_custom_target(lempar_cp ALL DEPENDS ${LEMPAR_DIR}/lempar.c)
add_dependencies(lemon lempar_cp)

# Install
if(NOT LEMON_SKIP_INSTALL)
  install(
    TARGETS lemon 
    EXPORT LemonTargets
    RUNTIME DESTINATION ${LEMON_INSTALL_BIN_DIR}
  )
  install(FILES ${CMAKE_CURRENT_SOURCE_DIR}/lempar.c DESTINATION ${LEMON_INSTALL_BIN_DIR})
endif(NOT LEMON_SKIP_INSTALL)


# Local Variables:
# tab-width: 8
# mode: cmake
# indent-tabs-mode: t
# End:
# ex: shiftwidth=2 tabstop=8
