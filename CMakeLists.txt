# Specify minimum CMake version required
cmake_minimum_required(VERSION 3.22)

# Define the submodule path (adjusted for your structure)
set(REPO2_PATH "${CMAKE_SOURCE_DIR}/repo4.0")

# Check if the path exists before executing the command
if(NOT EXISTS "${REPO2_PATH}")
    message(FATAL_ERROR "Submodule path '${REPO2_PATH}' does not exist. Please ensure the path is correct.")
endif()

# Step 1: Read expected tag/commit from .gitmodules
file(READ "${CMAKE_SOURCE_DIR}/.gitmodules" GITMODULES_CONTENTS)
string(REGEX MATCH "branch = ([^\n]+)" _ ${GITMODULES_CONTENTS})
set(EXPECTED_COMMIT "${CMAKE_MATCH_1}")

# Step 2: Get the current commit of Repo 2 by running a Git command
execute_process(
    COMMAND git rev-parse HEAD
    WORKING_DIRECTORY "${REPO2_PATH}"
    OUTPUT_VARIABLE CURRENT_COMMIT
    OUTPUT_STRIP_TRAILING_WHITESPACE
)

# Step 3: Compare the commits and stop the build if there's a mismatch
if(NOT "${CURRENT_COMMIT}" STREQUAL "${EXPECTED_COMMIT}")
    message(FATAL_ERROR "Repo 2 is at commit ${CURRENT_COMMIT}, but expected ${EXPECTED_COMMIT}. Please update the submodule to match the expected version.")
endif()

# Continue with other CMake configurations
