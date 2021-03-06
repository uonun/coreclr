project(superpmi-shim-simple)

remove_definitions(-DUNICODE)
remove_definitions(-D_UNICODE)

add_definitions(-DFEATURE_NO_HOST)
add_definitions(-DSELF_NO_HOST)

if(WIN32)
  #use static crt
  add_definitions(-MT)
endif(WIN32)

include_directories(.)
include_directories(../superpmi-shared)

set(SUPERPMI_SHIM_SIMPLE_SOURCES
    coreclrcallbacks.cpp
    jithost.cpp
    icorjitcompiler.cpp
    icorjitinfo.cpp
    ieememorymanager.cpp
    iexecutionengine.cpp
    superpmi-shim-simple.cpp
    ../superpmi-shared/callutils.cpp
    ../superpmi-shared/compileresult.cpp
    ../superpmi-shared/errorhandling.cpp
    ../superpmi-shared/logging.cpp
    ../superpmi-shared/mclist.cpp
    ../superpmi-shared/methodcontext.cpp
    ../superpmi-shared/methodcontextreader.cpp
    ../superpmi-shared/simpletimer.cpp
    ../superpmi-shared/spmiutil.cpp
    ../superpmi-shared/tocfile.cpp
    ../superpmi-shared/typeutils.cpp
    ../superpmi-shared/spmidumphelper.cpp
)

add_precompiled_header(
    standardpch.h
    ../superpmi-shared/standardpch.cpp
    SUPERPMI_SHIM_SIMPLE_SOURCES
)

if (WIN32)
    preprocess_def_file(${CMAKE_CURRENT_SOURCE_DIR}/superpmi-shim-simple.def ${CMAKE_CURRENT_BINARY_DIR}/superpmi-shim-simple.def)

    list(APPEND SUPERPMI_SHIM_SIMPLE_SOURCES ${CMAKE_CURRENT_BINARY_DIR}/superpmi-shim-simple.def)
endif (WIN32)

add_library(superpmi-shim-simple
    SHARED
    ${SUPERPMI_SHIM_SIMPLE_SOURCES}
)

if(CLR_CMAKE_PLATFORM_UNIX)
    target_link_libraries(superpmi-shim-simple
        utilcodestaticnohost
        mscorrc_debug
        coreclrpal
        palrt
    )
else()
    target_link_libraries(superpmi-shim-simple
        advapi32.lib
        ${STATIC_MT_CRT_LIB}
        ${STATIC_MT_CPP_LIB}
    )

    install (FILES ${CMAKE_CURRENT_BINARY_DIR}/$<CONFIG>/superpmi-shim-simple.pdb DESTINATION PDB)
endif(CLR_CMAKE_PLATFORM_UNIX)

install (TARGETS superpmi-shim-simple DESTINATION .)
