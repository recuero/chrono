#=============================================================================
# CMake configuration file for the test rig with continuous band track demo.
# This example program requires Irrlicht run-time visualization and the
# MKL or MUMPS Chrono modules.
#=============================================================================

# Irrlicht required
if(NOT ENABLE_MODULE_IRRLICHT)
    return()
endif()

# MUMPS or MKL required
if (NOT ENABLE_MODULE_MKL AND NOT ENABLE_MODULE_MUMPS)
    return()
endif()

#--------------------------------------------------------------
# List all model files for this demo

set(DEMOS
    demo_VEH_TrackTestRig_Band
)

#--------------------------------------------------------------
# Compiler and linker flags

include_directories(${CH_IRRLICHTINC})

set(COMPILER_FLAGS "${CH_CXX_FLAGS} ${CH_IRRLICHT_CXX_FLAGS} ${CH_MKL_CXX_FLAGS}")
set(LINKER_FLAGS "${CH_LINKERFLAG_EXE} ${CH_MKL_LINK_FLAGS}")
set(LIBRARIES 
    ChronoEngine
    ChronoEngine_irrlicht
    ChronoEngine_vehicle
    ChronoModels_vehicle
    )

if (ENABLE_MODULE_MKL)
    include_directories(${CH_MKL_INCLUDES})
    set(COMPILER_FLAGS "${COMPILER_FLAGS} ${CH_MKL_CXX_FLAGS}")
    set(LINKER_FLAGS "${LINKER_FLAGS} ${CH_MKL_LINK_FLAGS}")
    set(LIBRARIES ${LIBRARIES} ChronoEngine_mkl)
endif()

if (ENABLE_MODULE_MUMPS)
    include_directories(${CH_MUMPS_INCLUDES})
    set(LIBRARIES ${LIBRARIES} ChronoEngine_mumps)
endif()

#--------------------------------------------------------------
# Add executables

foreach(DEMO ${DEMOS})

    message(STATUS "...add ${DEMO}")
    
    add_executable(${DEMO} ${DEMO}.cpp)
    source_group("" FILES ${DEMO}.cpp)
    set_target_properties(${DEMO} PROPERTIES
                          FOLDER demos 
                          COMPILE_FLAGS "${COMPILER_FLAGS}"
                          LINK_FLAGS "${LINKER_FLAGS}")
    target_link_libraries(${DEMO} ${LIBRARIES})
    install(TARGETS ${DEMO} DESTINATION ${CH_INSTALL_DEMO})

endforeach()
