# Specify minimum CMake version required
cmake_minimum_required(VERSION 3.22)

# Define the project name (replace ProjectName with your actual project name)
project(MyProject)

# Define the submodule path (adjusted for your structure)
set(REPO2_PATH "${CMAKE_SOURCE_DIR}/repo4.0")

# Check if the path exists before executing the command
if(NOT EXISTS "${REPO2_PATH}")
    message(FATAL_ERROR "Submodule path '${REPO2_PATH}' does not exist. Please ensure the path is correct.")
endif()

# Step 1: Read expected tag from .gitmodules
file(READ "${CMAKE_SOURCE_DIR}/.gitmodules" GITMODULES_CONTENTS)

# Extract the tag information from .gitmodules
string(REGEX MATCH "tag = ([^\n]+)" _ ${GITMODULES_CONTENTS})
#message(FATAL_ERROR "${GITMODULES_CONTENTS}")

set(EXPECTED_TAG "${CMAKE_MATCH_1}") # 123

# If there's no tag found, give an error message
if(NOT EXPECTED_TAG)
    message(FATAL_ERROR "No tag specified in .gitmodules for repo4.0. Please define it to proceed.")
endif()

# Step 2: Get the current tag of Repo 2 by running a Git command
execute_process(
    COMMAND git describe --tags
    WORKING_DIRECTORY "${REPO2_PATH}"
    OUTPUT_VARIABLE CURRENT_TAG
    OUTPUT_STRIP_TRAILING_WHITESPACE
)

# Step 3: Compare the tags and stop the build if there's a mismatch
if(NOT "${CURRENT_TAG}" STREQUAL "${EXPECTED_TAG}")
    message(FATAL_ERROR "Repo 2 is at tag ${CURRENT_TAG}, but expected ${EXPECTED_TAG}. Please update the submodule to match the expected version.")
endif()

# Continue with other CMake configurations
