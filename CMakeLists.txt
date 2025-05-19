cmake_minimum_required(VERSION 3.16)

project(UnrealEngineSkyAtmosphere
    LANGUAGES CXX
)

add_subdirectory(tinyexr)


set(IMGUI_PATH ${CMAKE_CURRENT_SOURCE_DIR}/imgui)
add_library(ImGui STATIC
    ${IMGUI_PATH}/imgui.cpp
    ${IMGUI_PATH}/imgui_demo.cpp
    ${IMGUI_PATH}/imgui_draw.cpp
    ${IMGUI_PATH}/imgui_widgets.cpp
    ${IMGUI_PATH}/examples/imgui_impl_dx11.cpp
    ${IMGUI_PATH}/examples/imgui_impl_win32.cpp
)
target_include_directories(ImGui PUBLIC ${IMGUI_PATH})
target_compile_definitions(ImGui PUBLIC IMGUI_DISABLE_OBSOLETE_FUNCTIONS)


set(DX11BASE_PATH ${CMAKE_CURRENT_SOURCE_DIR}/DX11Base)
add_library(DX11Base
    ${DX11BASE_PATH}/Dx11Device.cpp
    ${DX11BASE_PATH}/WindowHelper.cpp
)
target_include_directories(DX11Base PUBLIC ${DX11BASE_PATH})
if (MSVC)
    target_compile_definitions(DX11Base PUBLIC UNICODE)

    # This ensures all member pointer variables are initialized to nullptr.
    # Without this `resetComPtr` function will cause a crash.
    target_compile_options(DX11Base PUBLIC /sdl)
endif()


set(APPLICATION_PATH ${CMAKE_CURRENT_SOURCE_DIR}/Application)
add_executable(Application
    ${APPLICATION_PATH}/DataRecord.cpp
    ${APPLICATION_PATH}/Game.cpp
    ${APPLICATION_PATH}/GpuDebugRenderer.cpp
    ${APPLICATION_PATH}/GpuDebugRenderer.cpp
    ${APPLICATION_PATH}/RenderSky.cpp
    ${APPLICATION_PATH}/RenderTerrain.cpp
    ${APPLICATION_PATH}/RenderWithLuts.cpp
    ${APPLICATION_PATH}/SkyAtmosphereCommon.cpp
    ${APPLICATION_PATH}/WinMain.cpp
)
target_include_directories(Application PUBLIC
    ${CMAKE_CURRENT_SOURCE_DIR}
    ${APPLICATION_PATH}
)
target_link_libraries(Application
    tinyexr
    ImGui
    DX11Base
)
if (MSVC)
    set_target_properties(Application PROPERTIES LINK_FLAGS "/SUBSYSTEM:WINDOWS")
endif()
