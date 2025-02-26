cmake_minimum_required(VERSION 3.17.0)
cmake_policy(VERSION 3.17.0)

# This file will build McStas and/or McXtrace
project(mccode C)

# Set module path
set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_CURRENT_SOURCE_DIR}/cmake/Modules/")

#Ensure various variables are set to sensible default values for the target
#platform (if not already set by a toolchain file):


# Setup McCode values (from mkdist or defaults)
include(MCUtil)


option( BUILD_MCSTAS "Build McStas" OFF )
option( BUILD_MCXTRACE "Build McXtrace" OFF )
option( BUILD_TOOLS "Include tools" ON )

if (${BUILD_MCSTAS} AND ${BUILD_MCXTRACE})
    message(WARNING "Configuration requests building both McStas and McXtrace which is likely to fail.")
elseif(NOT (${BUILD_MCSTAS} OR ${BUILD_MCXTRACE}))
    message(FATAL_ERROR "Configuration requires building one of McStas or McXtrace via -DBUILD_MCXTRACE=ON or -DBUILD_MCSTAS=ON")
endif()

if(${BUILD_MCSTAS})
    setupMCCODE("mcstas")
    message(STATUS "Configuring McStas build ${FLAVOR}")
    add_subdirectory(mcstas)
    add_subdirectory(mcstas-comps)
    if ( BUILD_TOOLS )
      add_subdirectory(tools)
    endif()
endif()

if (${BUILD_MCXTRACE})
    setupMCCODE("mcxtrace")
    message(STATUS "Configuring McXtrace build ${FLAVOR}")
    add_subdirectory(mcxtrace)
    add_subdirectory(mcxtrace-comps)
    if ( BUILD_TOOLS )
      add_subdirectory(tools)
    endif()
endif()

# Find/Fetch dependencies:

option( ENSURE_MCPL "Build Third Party code MCPL if not already available" ON )
if( ENSURE_MCPL )
  include( MCPL )
endif()

if ( BUILD_MCSTAS )
  option( ENSURE_NCRYSTAL "Build Third Party code NCrystal if not already available" ON )
  if( ENSURE_NCRYSTAL )
    include( NCrystal )
  endif()
endif()
