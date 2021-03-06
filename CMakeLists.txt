# This project represents the minial setup required to set
cmake_minimum_required(VERSION 3.21)
project(testImgui)

set(CMAKE_CXX_STANDARD 20)

# Fetch external content
include(FetchContent)
# Fetch DearImGui into _deps
# --------------------------
FetchContent_Declare(imgui
        GIT_REPOSITORY https://github.com/ocornut/imgui.git
        )
FetchContent_MakeAvailable(imgui)
if(imgui_POPULATED)
    message(STATUS "${Green}ImGui should be downloaded! ${ColourReset}")
endif()

# Fetch GLFW into _deps
# ---------------------
FetchContent_Declare(glfw
        GIT_REPOSITORY https://github.com/glfw/glfw.git
        )
if(NOT glfw_POPULATED)
    FetchContent_Populate(glfw)
    # Importent GLFW cmake flags
    set(GLFW_BUILD_DOCS OFF CACHE BOOL "" FORCE)
    set(GLFW_BUILD_TESTS OFF CACHE BOOL "" FORCE)
    set(GLFW_BUILD_EXAMPLES OFF CACHE BOOL "" FORCE)
    add_subdirectory(${glfw_SOURCE_DIR} ${glfw_BINARY_DIR})
endif()
message(STATUS "${Green}GLFW should be downloaded! ${ColourReset}")

# Fetch Glad into _deps
# ---------------------
FetchContent_Declare(extern_glad
        GIT_REPOSITORY https://github.com/Dav1dde/glad.git
        )
FetchContent_GetProperties(extern_glad)
if(NOT extern_glad_POPULATED)
    FetchContent_Populate(extern_glad)
    # Set the glad options
    # you can select the appropriate options from this page:
    # https://glad.dav1d.de/
    # and then search through the glad-cmake file to find the right commands to set here:
    set(GLAD_PROFILE "core")
    set(GLAD_API "gl=3.3")
    set(GLAD_INSTALL ON)
    add_subdirectory(
            ${extern_glad_SOURCE_DIR}
            ${extern_glad_BINARY_DIR}
            EXCLUDE_FROM_ALL
    )
endif()
message(STATUS "${Green}GLAD should be downloaded and configured! ${ColourReset}")

# Set up the imgui library
# -------------------------------
# ImGui does not come with cmake support, so i just create the library here
set(imgui_src
        # Top-Level files
        ${imgui_SOURCE_DIR}/imgui.cpp
        ${imgui_SOURCE_DIR}/imgui_demo.cpp
        ${imgui_SOURCE_DIR}/imgui_tables.cpp
        ${imgui_SOURCE_DIR}/imgui_widgets.cpp
        ${imgui_SOURCE_DIR}/imgui_draw.cpp

        # Backend-Specific code
        ${imgui_SOURCE_DIR}/backends/imgui_impl_glfw.cpp
        ${imgui_SOURCE_DIR}/backends/imgui_impl_opengl3.cpp)
set(imgui_inc
        # Top-Level Includes
        ${imgui_SOURCE_DIR}/
        # Backend-specific includes
        ${imgui_SOURCE_DIR}/backends/
        # Need to know where to find glfw
        ${glfw_SOURCE_DIR}/include/
        )
add_library(imgui ${imgui_src})
include_directories(${imgui_inc})
target_include_directories(imgui PUBLIC ${imgui_inc})

# Set up the project
include_directories(include)
set(src main.cpp)

add_executable(testImgui ${src})
# Link the libraries
target_link_libraries(testImgui glad glfw imgui)
