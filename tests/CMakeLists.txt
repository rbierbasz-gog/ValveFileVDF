cmake_minimum_required (VERSION 2.8.7)
project (vdf-Parser)

set(SRCS
 "main.cpp"
 "vdf_parser_test.cpp"
 "../Readme.md")

add_executable(tests "../vdf_parser.hpp" ${SRCS})

find_path(
    CATCH_INCLUDE_DIR 
    NAMES catch.hpp
    DOC "Catch include dir"
)
if (NOT CATCH_INCLUDE_DIR)
    include(ExternalProject)
    find_package(Git REQUIRED)
	
	set(BRANCH "master")
	if (MSVC_VERSION LESS 1900)
		set(BRANCH "Catch1.x") #catch2 needs C++11
	endif()
    
    ExternalProject_Add(
        catch
        PREFIX ${CMAKE_BINARY_DIR}/catch
        GIT_REPOSITORY https://github.com/philsquared/Catch.git
		GIT_TAG ${BRANCH}
        TIMEOUT 10
        UPDATE_COMMAND ${GIT_EXECUTABLE} pull
        CONFIGURE_COMMAND ""
        BUILD_COMMAND ""
        INSTALL_COMMAND ""
        LOG_DOWNLOAD ON
    )
    ExternalProject_Get_Property(catch source_dir)
    set(CATCH_INCLUDE_DIR ${source_dir}/single_include CACHE INTERNAL "Path to include folder for Catch.")
    add_dependencies(tests catch)
endif()

target_include_directories(tests PUBLIC ${CATCH_INCLUDE_DIR} "../")
set_property(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR} PROPERTY VS_STARTUP_PROJECT tests) #requires cmake 3.6
add_definitions("-DSOURCE_DIR=\"${CMAKE_SOURCE_DIR}\"")

include(CTest)
enable_testing(true)
add_test(NAME vdf_tests COMMAND tests)

