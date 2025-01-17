cmake_minimum_required(VERSION 3.14)
project(adlib2vgm LANGUAGES CXX C)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

set (BUILD_SHARED_LIBS OFF)

set (CMAKE_CXX_FLAGS "-DDEBUG -Wall")
if (WIN32)
	set(CMAKE_MSVC_RUNTIME_LIBRARY "MultiThreaded$<$<CONFIG:Debug>:Debug>")
else()
	set (CMAKE_EXE_LINKER_FLAGS "-static")
	set (CMAKE_EXE_LINKER_FLAGS_RELEASE "-static -s")
endif()

include_directories("binio" "utfcpp" "zstr")

file(GLOB_RECURSE SOURCES_BINIO "binio/*.cpp")
file(GLOB_RECURSE SOURCES_FMTS "Formats/*.cpp")
file(GLOB_RECURSE SOURCES_FMTS_DEPS "deps/*.c")
file(GLOB_RECURSE SOURCES_FAKE_ADPLUG "FakeAdplug/*.cpp")

set(ZLIB_USE_STATIC_LIBS ON)
if (WIN32)
	set(CMAKE_FIND_LIBRARY_SUFFIXES .lib)
	set(VCPKG_TARGET_ARCHITECTURE x86)
	set(VCPKG_CRT_LINKAGE static)
	set(VCPKG_LIBRARY_LINKAGE static)
else()
	set(CMAKE_FIND_LIBRARY_SUFFIXES .a)
endif()
find_package(ZLIB REQUIRED)

add_executable(adlib2vgm
	${SOURCES_BINIO}
	${SOURCES_GZSTREAM}
	${SOURCES_FAKE_ADPLUG}
	${SOURCES_FMTS_DEPS}
	${SOURCES_FMTS}
	main.cpp
	VgmOpl.cpp
)

target_include_directories(adlib2vgm PRIVATE "deps")

target_link_libraries(adlib2vgm PRIVATE ZLIB::ZLIB)
