#if building standalone and not specified build tests
option(BUILD_TESTS "Whether to build tests" ON)
include(FetchContent)
FetchContent_Declare( al2o3 GIT_REPOSITORY https://github.com/DeanoC/al2o3 GIT_TAG master )
FetchContent_GetProperties(al2o3)
if(NOT al2o3_POPULATED)
    FetchContent_Populate(al2o3)
    add_subdirectory(${al2o3_SOURCE_DIR} ${al2o3_BINARY_DIR})
endif()

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
ADD_LIB(${LibName} "${Interface}" "${Src}" "${Deps}")

if( ENKITS_TASK_PRIORITIES_NUM GREATER "0" )
    target_compile_definitions( ${LibName} PUBLIC "ENKITS_TASK_PRIORITIES_NUM=${ENKITS_TASK_PRIORITIES_NUM}" )
endif()