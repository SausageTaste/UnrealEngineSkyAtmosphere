cmake_minimum_required(VERSION 3.16)

project(UnrealEngineSkyAtmosphere
    LANGUAGES CXX
)


add_subdirectory(tinyexr)



set(IMGUI_PATH ${CMAKE_CURRENT_SOURCE_DIR}/imgui)
set(IMGUI_SOURCES
    ${IMGUI_PATH}/imgui.cpp
    ${IMGUI_PATH}/imgui_demo.cpp
    ${IMGUI_PATH}/imgui_draw.cpp
    ${IMGUI_PATH}/imgui_widgets.cpp
    ${IMGUI_PATH}/examples/imgui_impl_dx11.cpp
    ${IMGUI_PATH}/examples/imgui_impl_win32.cpp
)
add_library(ImGui STATIC ${IMGUI_SOURCES} ${IMGUI_EXAMPLES_SOURCES})
target_include_directories(ImGui PUBLIC ${IMGUI_PATH})
target_compile_definitions(ImGui PUBLIC
    IMGUI_DISABLE_OBSOLETE_FUNCTIONS
)


add_library(DX11Base
    ${CMAKE_CURRENT_SOURCE_DIR}/DX11Base/Dx11Device.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/DX11Base/WindowHelper.cpp
)
target_include_directories(DX11Base PUBLIC
    ${CMAKE_CURRENT_SOURCE_DIR}/DX11Base
)
target_compile_definitions(DX11Base PUBLIC
    UNICODE
)
if (MSVC)
    # This ensures all member pointer variables are initialized to nullptr.
    # Without this `resetComPtr` function will cause a crash.
    target_compile_options(DX11Base PUBLIC /sdl)
endif()


add_executable(Application
    ${CMAKE_CURRENT_SOURCE_DIR}/Application/DataRecord.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/Application/Game.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/Application/GpuDebugRenderer.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/Application/GpuDebugRenderer.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/Application/RenderSky.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/Application/RenderTerrain.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/Application/RenderWithLuts.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/Application/SkyAtmosphereCommon.cpp
    ${CMAKE_CURRENT_SOURCE_DIR}/Application/WinMain.cpp
)
target_include_directories(Application PUBLIC
    ${CMAKE_CURRENT_SOURCE_DIR}
    ${CMAKE_CURRENT_SOURCE_DIR}/Application
)
target_link_libraries(Application
    tinyexr
    ImGui
    DX11Base
)
set_target_properties(Application PROPERTIES
    LINK_FLAGS "/SUBSYSTEM:WINDOWS"
)
