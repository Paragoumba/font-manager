cmake_minimum_required(VERSION 3.0)
project(node_fontmanager)

set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_SOURCE_DIR}/cmake")

include(NodeJS)
include(CPack)

nodejs_init()

add_nodejs_module(node_fontmanager
	${node_fontmanager_SOURCE_DIR}/src/FontDescriptor.h
	${node_fontmanager_SOURCE_DIR}/src/FontManager.cc
	${NODEJS_SOURCES})

target_link_libraries(node_fontmanager ${NODEJS_LIBRARIES})
target_include_directories(node_fontmanager PUBLIC ${NODEJS_INCLUDE_DIRS})

set(node_fontmanager_EXTRAS
	${node_fontmanager_SOURCE_DIR}/main.js
	${node_fontmanager_SOURCE_DIR}/package.json)

if(CMAKE_HOST_WIN32)
	target_sources(node_fontmanager
		PUBLIC ${node_fontmanager_SOURCE_DIR}/src/FontManagerWindows.cc)

	target_link_libraries(node_fontmanager Dwrite)

	target_compile_definitions(node_fontmanager
		PRIVATE -D_CRT_SECURE_NO_WARNINGS)
elseif(CMAKE_HOST_APPLE)
	target_sources(node_fontmanager
		PUBLIC ${node_fontmanager_SOURCE_DIR}/src/FontManagerMac.mm)

	target_link_libraries(node_fontmanager
		CoreText.framework
		Foundation.framework)
elseif(CMAKE_HOST_UNIX)
	target_sources(node_fontmanager
		PUBLIC ${node_fontmanager_SOURCE_DIR}/src/FontManagerLinux.mm)

	target_link_libraries(node_fontmanager fontconfig)
endif()

install(FILES $<TARGET_PDB_FILE:node_fontmanager> DESTINATION . OPTIONAL)
install(FILES $<TARGET_FILE:node_fontmanager> DESTINATION .)
install(FILES ${node_fontmanager_EXTRAS} DESTINATION .)