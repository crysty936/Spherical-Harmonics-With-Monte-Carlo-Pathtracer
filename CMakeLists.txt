# run "cmake .." in the build folder

cmake_minimum_required(VERSION 3.10)

# set the project name and version
project(SecondEngineProject VERSION 1.0)

# specify the C++ standard
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED True)

set_property(GLOBAL PROPERTY USE_FOLDERS ON)

# Make sure all dll's are copied to the executable folder
#set (CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR})
#set (CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR})

# add Glad
add_subdirectory(Plugins/glad)
list(APPEND extra_libs glad)

# EASTL
add_subdirectory(Plugins/EASTL)
list(APPEND extra_libs EASTL)

# ASSIMP
add_subdirectory(Plugins/assimp)
list(APPEND extra_libs assimp)

# Add stb_image
list(APPEND EXTRA_INCLUDES "${CMAKE_CURRENT_LIST_DIR}/Plugins/stb_image/")

# Add GLM
list(APPEND EXTRA_INCLUDES "${CMAKE_CURRENT_LIST_DIR}/Plugins/glm/")

# Add ImGui
add_subdirectory(Plugins/imgui)
list(APPEND extra_libs imgui)

# Rendering API's libs
list(APPEND extra_libs opengl32.lib)
list(APPEND extra_libs d3d11.lib)
list(APPEND extra_libs d3dcompiler.lib)

# used to add all .cpp and .h files under source
file(GLOB_RECURSE source_files CONFIGURE_DEPENDS "${CMAKE_CURRENT_LIST_DIR}/Engine/*.cpp" "${CMAKE_CURRENT_LIST_DIR}/Engine/*.h")

# used to create filters for all files one to one with their folder structure
source_group(TREE ${CMAKE_CURRENT_LIST_DIR} FILES ${source_files})

# OBJECT to make sure that all the project's files and statics are used and linked as if they were from the executable's project, that is, no matter if they are used or not.( Eg. for internal 
# plugins which are not referenced anywhere but self-register.

add_library(SecondEngineProject STATIC ${source_files})

target_link_libraries(SecondEngineProject PUBLIC ${extra_libs})

target_link_libraries(SecondEngineProject PRIVATE "d3d12.lib")
target_link_libraries(SecondEngineProject PRIVATE "dxgi.lib")

list(APPEND EXTRA_INCLUDES ${CMAKE_CURRENT_LIST_DIR}/Engine/Source)	

target_include_directories(SecondEngineProject PUBLIC
                           ${EXTRA_INCLUDES}
                           )
						   

						   

# Stop it from creating unnecessary Project
set(CMAKE_SUPPRESS_REGENERATION true)

# GTest done so that it can be used with lib and .exe by creating a copy of the original project, without the main

# Build with MDd
set(gtest_force_shared_crt ON CACHE BOOL "" FORCE)

# Do not create GMock project
option(BUILD_GMOCK OFF)


# Google Test

# ADD_SUBDIRECTORY(Plugins/googletest-main)

# enable_testing()

# include_directories(${gtest_SOURCE_DIR}/include ${gtest_SOURCE_DIR})

# include(GoogleTest)

# list(APPEND source_files UnitTests/test.cpp)

# # Remove the original entry point so that the test one takes it's place
# get_filename_component(full_path_test_cpp ${CMAKE_CURRENT_SOURCE_DIR}/Engine/Source/Core/EntryPoint.cpp ABSOLUTE)

# list(REMOVE_ITEM source_files "${full_path_test_cpp}")
# add_executable(EngineTests ${source_files})

# target_include_directories(EngineTests PUBLIC
                           # "${PROJECT_BINARY_DIR}"
                           # ${EXTRA_INCLUDES}
                           # )



# target_link_libraries(EngineTests PUBLIC gtest gtest_main ${extra_libs})

# gtest_discover_tests(EngineTests)

# Group Plugins in a separate folder
set_target_properties(	glad assimp imgui EASTL EAStdC EAAssert EAThread 
						PROPERTIES
						FOLDER "Plugins")		  
						
						

