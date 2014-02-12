cmake_minimum_required(VERSION 2.8.9 FATAL_ERROR)

#-----------------------------------------------------------------------
# CPM configuration
#-----------------------------------------------------------------------
set(CPM_MODULE_NAME boost)
set(CPM_LIB_TARGET_NAME ${CPM_MODULE_NAME})

if ((DEFINED CPM_DIR) AND (DEFINED CPM_UNIQUE_ID) AND (DEFINED CPM_TARGET_NAME))
  set(CPM_LIB_TARGET_NAME ${CPM_TARGET_NAME})
  set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} ${CPM_DIR})
  include(CPM)
else()
  set(CPM_DIR "${CMAKE_CURRENT_BINARY_DIR}/cpm-packages" CACHE TYPE STRING)
  find_package(Git)
  if(NOT GIT_FOUND)
    message(FATAL_ERROR "CPM requires Git.")
  endif()
  if (NOT EXISTS ${CPM_DIR}/CPM.cmake)
    execute_process(
      COMMAND "${GIT_EXECUTABLE}" clone https://github.com/iauns/cpm ${CPM_DIR}
      RESULT_VARIABLE error_code
      OUTPUT_VARIABLE head_sha)
    if(error_code)
      message(FATAL_ERROR "CPM failed to get the hash for HEAD")
    endif()
  endif()
  include(${CPM_DIR}/CPM.cmake)
endif()

# All externals *must* define this.
CPM_ForceOnlyOneModuleVersion()

CPM_InitModule(${CPM_MODULE_NAME})

#------------------------------------------------------------------------------
# boost graph library
#------------------------------------------------------------------------------

CPM_EnsureRepoIsCurrent(
    TARGET_DIR "${CMAKE_CURRENT_SOURCE_DIR}/boost"            # Required - Directory in which to place repository.
    GIT_REPOSITORY "https://github.com/boostorg/boost.git"         # Git repository to clone and keep up to date.
    GIT_TAG  "boost-1.55.0"               # Git tag to checkout.   
    USE_CACHING 1              # Enables caching of repositories if the user 
                                 # has specified CPM_MODULE_CACHING_DIR.
                                 # Not enabled by default.
    )


CPM_ExportAdditionalIncludeDir("${CMAKE_CURRENT_SOURCE_DIR}/boost")