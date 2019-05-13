cmake_minimum_required (VERSION 2.6)
project (tap C)

option (LIBTAP_TESTS "Build libtap tests" ON)
option (BUILD_STATIC_LIBRARY "Whether to build the static library (which takes more time)" ON)

include_directories (
	${CMAKE_CURRENT_SOURCE_DIR})

add_library (tap
    SHARED
	tap.c
	tap.h)

set( TAP_LIBS "tap")
IF (BUILD_STATIC_LIBRARY)
    ADD_LIBRARY (tap-static
        STATIC
        tap.c
        tap.h
    )
    LIST (APPEND TAP_LIBS "tap-static")

    SET_TARGET_PROPERTIES(
        tap-static
        PROPERTIES OUTPUT_NAME "tap"
    )
    # See https://en.wikipedia.org/wiki/Reproducible_builds
    # https://reproducible-builds.org/ .
    SET(_determinstic_build $ENV{REPRODUCIBLE_BUILDS})
    IF (_determinstic_build)
        SET(CMAKE_RANLIB ":")
        SET_TARGET_PROPERTIES(
            tap-static
            PROPERTIES STATIC_LIBRARY_OPTIONS "-D"
        )
    ENDIF ()
    SET_TARGET_PROPERTIES(
        ${TAP_LIBS}
        PROPERTIES CLEAN_DIRECT_OUTPUT 1
    )
ENDIF ()
INSTALL(
    TARGETS ${TAP_LIBS}
)
install (FILES tap.h
	DESTINATION "include")

if (LIBTAP_TESTS)
	enable_testing ()
	add_subdirectory (t)
	add_test (prove
		${CMAKE_COMMAND} -E chdir ${CMAKE_CURRENT_SOURCE_DIR} prove)
    ADD_CUSTOM_TARGET(
        "check"
        "make" "test"
    )
endif ()
