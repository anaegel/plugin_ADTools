cmake_minimum_required(VERSION 2.8)
include(ExternalProject)

set(pluginName ADTools)
project(UG_PLUGIN_${pluginName})

# Include the definitions and dependencies for ug-plugins.
include(${UG_ROOT_CMAKE_PATH}/ug_plugin_includes.cmake)

# Create custom configure.
add_custom_target(
  adolc-autoconf
  ${SHELL_EXECUTABLE} autoreconf -fi
  WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/adol-c
)

# External configure, make & install
# The following lines install the library to $UG4_ROOT/lib.
# TODO: Adjust CC and CXX
ExternalProject_Add(adolc-external
    SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/adol-c
   
    #--Configure step-------------
   	CONFIGURE_COMMAND ${CMAKE_CURRENT_SOURCE_DIR}/adol-c/configure --includedir=${CMAKE_CURRENT_SOURCE_DIR}/include --libdir=${CMAKE_ARCHIVE_OUTPUT_DIRECTORY} 
   
   	#--Build step-----------------
    BUILD_COMMAND ${MAKE}
    
    #--Install step---------------
  	# INSTALL_DIR ${CMAKE_ARCHIVE_OUTPUT_DIRECTORY} # Instgall 
  	# BUILD_COMMAND ${MAKE} install
)

add_dependencies(adolc-external adolc-autoconf)
   
# Add header to include path   
add_library(adolc INTERFACE)
add_dependencies(adolc adolc-external)
target_include_directories(adolc INTERFACE ${CMAKE_CURRENT_SOURCE_DIR}/include)
target_link_libraries(adolc INTERFACE -ladolc)
# include_directories(AFTER ${CMAKE_CURRENT_SOURCE_DIR}/include)

add_dependencies(adolc adolc-external autoconf)

# Copy library (DEPRECATED)
#add_custom_target(
#  libadolc
#  ${SHELL_EXECUTABLE} cp ${CMAKE_CURRENT_SOURCE_DIR}/ ${CMAKE_ARCHIVE_OUTPUT_DIRECTORY}
#  WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}
#)