cmake_minimum_required(VERSION 2.8.9 FATAL_ERROR)

#-----------------------------------------------------------------------
# CPM configuration
#-----------------------------------------------------------------------
set(CPM_MODULE_NAME boost)
# initialize
if(NOT EXISTS ${CMAKE_CURRENT_BINARY_DIR}/init.cmake)
  file(DOWNLOAD https://raw.github.com/toeb/cpm/master/init-cpm-module.cmake ${CMAKE_CURRENT_BINARY_DIR}/init.cmake)
endif()
include(${CMAKE_CURRENT_BINARY_DIR}/init.cmake)

# All externals *must* define this.
CPM_ForceOnlyOneModuleVersion()

CPM_InitModule(${CPM_MODULE_NAME})

#------------------------------------------------------------------------------
# boost library
#------------------------------------------------------------------------------

message(STATUS "downloading boost libraries")
CPM_EnsureRepoIsCurrent(
    TARGET_DIR "${CMAKE_CURRENT_SOURCE_DIR}/boost"            # Required - Directory in which to place repository.
    GIT_REPOSITORY "https://github.com/boostorg/boost.git"         # Git repository to clone and keep up to date.
    GIT_TAG  "boost-1.55.0"               # Git tag to checkout.   
    USE_CACHING 1              # Enables caching of repositories if the user 
                                 # has specified CPM_MODULE_CACHING_DIR.
                                 # Not enabled by default.
    )

message(STATUS "adding boost libraries")

macro(cpm_boost_subdirlist result curdir)
  file(GLOB children RELATIVE ${curdir} ${curdir}/*)
  set(dirlist "")
  foreach(child ${children})
    if(IS_DIRECTORY ${curdir}/${child})
        set(dirlist ${dirlist} ${child})
    endif()
  endforeach()
  set(${result} ${dirlist})
endmacro()

cpm_boost_subdirlist(cpm_boost_subdirs "${CMAKE_CURRENT_SOURCE_DIR}/libs")

foreach(subdir ${cpm_boost_subdirs})
  if(EXISTS "${CMAKE_CURRENT_SOURCE_DIR}/libs/${subdir}/include")  
    message(STATUS "adding boost::${subdir}")
    CPM_ExportAdditionalIncludeDir("${CMAKE_CURRENT_SOURCE_DIR}/libs/${subdir}/include")
  endif()
endforeach()