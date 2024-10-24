cmake_minimum_required(VERSION 3.10)
project(MainProject)

# Specify the expected tag of the submodule directly
set(SUBMODULE_TAG "v1.0.0")  # Change this to your expected submodule tag

# Check if the current directory is a git repository
execute_process(
    COMMAND git rev-parse --is-inside-work-tree
    WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}
    RESULT_VARIABLE GIT_CHECK
)

if(NOT GIT_CHECK EQUAL 0)
    message(FATAL_ERROR "This directory is not a Git repository!")
endif()

# Get the current tag of the submodule
execute_process(
    COMMAND git -C submodule/ describe --tags
    OUTPUT_VARIABLE CURRENT_TAG
    OUTPUT_STRIP_TRAILING_WHITESPACE
)

# Check if the current tag matches the expected tag
if(NOT CURRENT_TAG STREQUAL SUBMODULE_TAG)
    message(FATAL_ERROR "Submodule tag mismatch! Expected: ${SUBMODULE_TAG}, Found: ${CURRENT_TAG}")
endif()

# Add the subdirectory for the submodule
add_subdirectory(submodule)

# Create the main executable
add_executable(main main.c)
target_link_libraries(main submodule)