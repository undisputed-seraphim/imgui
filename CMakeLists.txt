cmake_minimum_required(VERSION 3.8)
project(imgui_examples C CXX)

set(CMAKE_MODULE_PATH ${CMAKE_CURRENT_SOURCE_DIR}/cmake)

find_package(glfw3 3.3)
find_package(OpenGL)
find_package(SDL2 2.0)
find_package(Vulkan 1.1)

include_directories(${CMAKE_CURRENT_SOURCE_DIR})
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/examples)

add_library(imgui STATIC
	"imgui.cpp"
	"imgui_draw.cpp"
	"imgui_demo.cpp"
	"imgui_tables.cpp"
	"imgui_widgets.cpp"
)

if (glfw3_FOUND)
	add_library(impl_glfw STATIC 
		"backends/imgui_impl_glfw.cpp"
		"backends/imgui_impl_glfw.h"
	)
	target_link_libraries(impl_glfw PUBLIC glfw imgui)
endif()

if (SDL2_FOUND)
	add_library(impl_sdl STATIC
		"backends/imgui_impl_sdl.cpp"
		"backends/imgui_impl_sdl.h"
	)
	target_compile_definitions(impl_sdl PUBLIC SDL_MAIN_HANDLED)
	target_link_libraries(impl_sdl PUBLIC SDL2::SDL2 imgui)
endif()

if (OpenGL_FOUND)
	find_package(GLEW REQUIRED)
	
	add_library(impl_opengl STATIC
		"backends/imgui_impl_opengl3.cpp"
		"backends/imgui_impl_opengl3.h"
	)
	target_link_libraries(impl_opengl PUBLIC OpenGL::GL GLEW::GLEW)
	
	if (SDL2_FOUND)
		add_executable(sdl_opengl3 "examples/example_sdl_opengl3/main.cpp")
		target_link_libraries(sdl_opengl3 impl_opengl impl_sdl)
	endif()

	if (glfw3_FOUND)
		add_executable(glfw_opengl3 "examples/example_glfw_opengl3/main.cpp")
		target_link_libraries(glfw_opengl3 impl_opengl impl_glfw)
	endif()
endif()

if (Vulkan_FOUND)
	add_library(impl_vulkan STATIC
		"backends/imgui_impl_vulkan.cpp"
		"backends/imgui_impl_vulkan.h"
	)
	target_link_libraries(impl_vulkan PUBLIC Vulkan::Vulkan)

	if (SDL2_FOUND)
		add_executable(sdl_vulkan "examples/example_sdl_vulkan/main.cpp")
		target_link_libraries(sdl_vulkan impl_vulkan impl_sdl)
	endif()

	if (glfw3_FOUND)
		add_executable(glfw_vulkan "examples/example_glfw_vulkan/main.cpp")
		target_link_libraries(glfw_vulkan impl_vulkan impl_glfw)
	endif()
endif()