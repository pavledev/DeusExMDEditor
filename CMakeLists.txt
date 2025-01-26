cmake_minimum_required(VERSION 3.8)

project("DeusExMDEditor" C CXX)

set(CMAKE_CXX_STANDARD 23)
set(VCPKG_BUILD_ROOT "${VCPKG_INSTALLED_DIR}/${VCPKG_TARGET_TRIPLET}")

add_subdirectory(External/imgui-docking-layout)
add_subdirectory(External/imguizmo)
add_subdirectory(External/GLTFSDK)
add_subdirectory(External/HLSLDecompiler)
add_subdirectory(External/ImGuiColorTextEdit)

find_package(directxtex CONFIG REQUIRED)
find_package(directxtk CONFIG REQUIRED)
find_package(RapidJSON CONFIG REQUIRED)
find_package(SFML COMPONENTS audio CONFIG REQUIRED)
find_package(effects11 CONFIG REQUIRED)

file(GLOB_RECURSE SRC_FILES
	CONFIGURE_DEPENDS
	src/*.cpp
	src/*.c
	src/*.hpp
	src/*.h,
)

file(GLOB_RECURSE HEADER_FILES
	CONFIGURE_DEPENDS
	include/*.h
)

add_executable(DeusExMDEditor
	${SRC_FILES}
	${HEADER_FILES}
)

target_compile_definitions(DeusExMDEditor PRIVATE
	INTERFACE TRANSPOSED_MAT33=1
)

target_include_directories(DeusExMDEditor PRIVATE
	${CMAKE_SOURCE_DIR}/External/mINI/src/mini
)

target_include_directories(DeusExMDEditor PUBLIC
	${CMAKE_CURRENT_SOURCE_DIR}/include
	${CMAKE_SOURCE_DIR}/External/IconFontCppHeaders
	${CMAKE_CURRENT_SOURCE_DIR}/External/PhysX-SDK
	${CMAKE_CURRENT_SOURCE_DIR}/External/PhysX-SDK/Foundation
	${CMAKE_CURRENT_SOURCE_DIR}/External/PhysX-SDK/Physics
)

target_link_libraries(DeusExMDEditor PUBLIC
	d3d11
	dxgi
	Microsoft::DirectXTK
    Microsoft::DirectXTex
	imgui-docking-layout
	imguizmo
	rapidjson
	GLTFSDK
	${CMAKE_CURRENT_SOURCE_DIR}/lib/x64/NxCharacter64.lib
	${CMAKE_CURRENT_SOURCE_DIR}/lib/x64/PhysXCooking64.lib
	${CMAKE_CURRENT_SOURCE_DIR}/lib/x64/PhysXCore64.lib
	${CMAKE_CURRENT_SOURCE_DIR}/lib/x64/PhysXLoader64.lib
	${CMAKE_CURRENT_SOURCE_DIR}/lib/x64/PhysXLoader64.lib
	sfml-audio
	Microsoft::Effects11
	hlsl-decompiler
	imgui-color-text-edit
)

install(TARGETS DeusExMDEditor
	RUNTIME DESTINATION bin
	LIBRARY DESTINATION lib
	ARCHIVE DESTINATION lib
)

add_custom_command(TARGET DeusExMDEditor POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E copy_directory
    "${CMAKE_SOURCE_DIR}/bin/x64/"
    $<TARGET_FILE_DIR:DeusExMDEditor>)

add_custom_command(TARGET DeusExMDEditor POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E copy_directory
    "${CMAKE_SOURCE_DIR}/assets/"
    $<TARGET_FILE_DIR:DeusExMDEditor>/assets)