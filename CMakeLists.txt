cmake_minimum_required(VERSION 3.12.4)
get_directory_property(hasParent PARENT_DIRECTORY)
if(NOT hasParent)
	option(unittests "unittests" OFF)
	get_filename_component(_PARENT_DIR ${CMAKE_CURRENT_SOURCE_DIR} DIRECTORY)
	set_property(GLOBAL PROPERTY GLOBAL_FETCHDEPS_BASE ${_PARENT_DIR}/al2o3 )
	include(FetchContent)
	FetchContent_Declare( al2o3 GIT_REPOSITORY https://github.com/DeanoC/al2o3 GIT_TAG master )
	FetchContent_GetProperties(al2o3)
	if(NOT al2o3_POPULATED)
		FetchContent_Populate(al2o3)
		add_subdirectory(${al2o3_SOURCE_DIR} ${al2o3_BINARY_DIR})
	endif()
	INIT_AL2O3(${CMAKE_CURRENT_SOURCE_DIR})
endif ()

SET_MIN_VERSIONS()
set(LibName al2o3_enki)
project(${LibName})
set( ENKITS_TASK_PRIORITIES_NUM "3" CACHE STRING "Number of task priorities, 1-5, 0 for defined by defaults in source" )


set(Interface
        LockLessMultiReadPipe.h
        TaskScheduler.h
        TaskScheduler_c.h
        )
set(Src
        TaskScheduler.cpp
        TaskScheduler_c.cpp
        )

set(Deps
        al2o3_platform
        al2o3_memory
        al2o3_tinystl)

if( ENKITS_BUILD_SHARED )
    add_library( enkiTS SHARED ${ENKITS_SRC} )
    target_compile_definitions( enkiTS PRIVATE ENKITS_BUILD_DLL=1 )
    target_compile_definitions( enkiTS INTERFACE ENKITS_DLL=1 )
    if( UNIX )
	    if ("${CMAKE_CXX_COMPILER_ID}" STREQUAL "GNU" OR "${CMAKE_CXX_COMPILER_ID}" STREQUAL "Clang") 
	         SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fvisibility=hidden")
    	endif()
	endif ()
else()
	ADD_LIB(${LibName} "${Interface}" "${Src}" "${Deps}")
endif()


if( ENKITS_TASK_PRIORITIES_NUM GREATER "0" )
    target_compile_definitions( ${LibName} PUBLIC "ENKITS_TASK_PRIORITIES_NUM=${ENKITS_TASK_PRIORITIES_NUM}" )
endif()
