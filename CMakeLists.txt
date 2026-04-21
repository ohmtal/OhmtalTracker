cmake_minimum_required(VERSION 3.16)
project(IncludeTestProject LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

# OhmFlux flags:
set(DISABLE_DEMOS ON CACHE BOOL "Disable engine demos" FORCE)
set(WITH_BOX2D OFF CACHE BOOL "Enable Box 2D : off" FORCE)

set(OHMFLUX_DIR "/opt/OhmFlux" CACHE PATH "Path to the OhmFlux engine source directory")


include(FetchContent)

if( ("${OHMFLUX_DIR}" STREQUAL "") OR (NOT EXISTS "${OHMFLUX_DIR}/CMakeLists.txt"))
    message(WARNING "The directory OHMFLUX_DIR='${OHMFLUX_DIR}' does not contain a CMakeLists.txt file! Fetching from github.")

    FetchContent_Declare(
        ohmFlux
        GIT_REPOSITORY https://github.com/ohmtal/OhmFlux
        GIT_TAG        main
    )
    FetchContent_MakeAvailable(ohmFlux)

    set(OHMFLUX_DIR ${ohmFlux_SOURCE_DIR})

else()
    add_subdirectory("${OHMFLUX_DIR}" "${CMAKE_BINARY_DIR}/ohmFlux_build")
endif()

set(SFX_DIR    "${OHMFLUX_DIR}/engine/sfx")
set(YMFM_DIR   "${OHMFLUX_DIR}/lib/ymfm/src")
set(OPL3_DIR   "${OHMFLUX_DIR}/engine/opl3")



set (SFX_SOURCES
    #  --- sfx ---
    ${SFX_DIR}/SFXGenerator.cpp
    ${SFX_DIR}/SFXGeneratorStereo.cpp
)

set( OPL_SOURCES
    #  --- ymfm ---
    ${YMFM_DIR}/ymfm_opl.cpp
    ${YMFM_DIR}/ymfm_misc.cpp
    ${YMFM_DIR}/ymfm_adpcm.cpp
    ${YMFM_DIR}/ymfm_pcm.cpp
    ${YMFM_DIR}/ymfm_ssg.cpp

    #  --- opl3 ---
    ${OPL3_DIR}/OPL3Controller.cpp
)




# --- Ohmtal Tracker ---
option(BUILD_FLUXTRACKER "Build Ohmtal Tracker" ON)
if(BUILD_FLUXTRACKER)
    set(TRACKER_DIR "${CMAKE_CURRENT_LIST_DIR}/OhmtalTracker")
    set(TRACKER_SOURCES
        "${TRACKER_DIR}/src/main.cpp"
        "${TRACKER_DIR}/src/otGui.cpp"
        "${TRACKER_DIR}/src/dspGui.cpp"
        "${TRACKER_DIR}/src/bankGui.cpp"
        "${TRACKER_DIR}/src/songGui.cpp"
        "${TRACKER_DIR}/src/songFunc.cpp"
        "${TRACKER_DIR}/src/keyboardGui.cpp"
        "${TRACKER_DIR}/src/fancyOverlays.cpp"
        "${TRACKER_DIR}/src/ordersGui.cpp"

        # --- OPL
        ${OPL_SOURCES}
        # --- sfx
        ${SFX_SOURCES}

    )
    configure_project_target(OhmtalTracker ${TRACKER_DIR} "${TRACKER_DIR}/assets@assets" "${TRACKER_SOURCES}")
endif()

