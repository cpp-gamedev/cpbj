cmake_minimum_required(VERSION 3.14)

# +++ PROJECT OPTIONS ++++
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_POLICY_DEFAULT_CMP0077 NEW)								# don't let OPTION erase non-cache variables
set(CMAKE_CXX_EXTENSIONS OFF CACHE INTERNAL "" FORCE) 				# disable compiler extensions
set(CMAKE_POSITION_INDEPENDENT_CODE ON)								# enable PIC flag
set(BUILD_SHARED_LIBS OFF)

# +++ DEFAULT BUILD TYPE +++
set(DEFAULT_BUILD_TYPE "Release")
if(EXISTS "${CMAKE_SOURCE_DIR}/.git")
	set(DEFAULT_BUILD_TYPE "Debug")
endif()

# +++ PROJECT PARAMETERS +++
set(PROJECT_NAME cpbj)
set(IS_ROOT_PROJECT OFF)
set(PROJECT_VERSION "0.0.1.0" CACHE STRING "Application Version" FORCE)
project(${PROJECT_NAME} VERSION "${PROJECT_VERSION}")

# +++ EXTERNAL LIBRARIES +++
add_subdirectory(ext/sfml)

if (CMAKE_PROJECT_NAME STREQUAL PROJECT_NAME)
	set(IS_ROOT_PROJECT ON)
endif()

set(CCJSON "${CMAKE_CURRENT_BINARY_DIR}/compile_commands.json")

if (EXISTS "${CCJSON}")
	message(STATUS "Copying ${CCJSON} to project root")
	file(COPY "${CCJSON}" DESTINATION "${PROJECT_SOURCE_DIR}")
endif()

# +++ GIT SUBMODULES +++
include(cmake/git.cmake)
find_package(Git QUIET)
if(GIT_FOUND AND EXISTS "${CMAKE_CURRENT_SOURCE_DIR}/.git")
	option(PKMN_GIT_UPDATE_SUBMODULES "Init/Update submodules during generation" ON)
	if(PKMN_GIT_UPDATE_SUBMODULES)
		git_update_submodules(FATAL_ERROR)
	endif()
endif()

# +++ EXECUTABLE +++
file(GLOB_RECURSE SOURCES CONFIGURE_DEPENDS "${CMAKE_CURRENT_SOURCE_DIR}/src/*.?pp")
add_executable(${PROJECT_NAME} ${SOURCES})
target_link_libraries(${PROJECT_NAME} PRIVATE sfml-graphics)
target_compile_features(${PROJECT_NAME} PRIVATE cxx_std_17)

# +++ COMPILE OPTIONS +++
if(CMAKE_CXX_COMPILER_ID STREQUAL MSVC)
  target_compile_options(${PROJECT_NAME} PRIVATE /MP $<$<NOT:$<CONFIG:Debug>>:/WX>)
elseif(CMAKE_CXX_COMPILER_ID STREQUAL Clang OR CMAKE_CXX_COMPILER_ID STREQUAL GNU)
  target_compile_options(${PROJECT_NAME} PRIVATE -Wall -Wextra $<$<NOT:$<CONFIG:Debug>>:-Werror>)
endif()
