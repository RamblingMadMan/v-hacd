#set(CMAKE_INSTALL_PREFIX "${PROJECT_BINARY_DIR}/output" CACHE PATH "project install prefix" FORCE)
set(CMAKE_COMMON_INC "${CMAKE_CURRENT_SOURCE_DIR}/../scripts/cmake_common.cmake")
add_subdirectory ("${CMAKE_CURRENT_SOURCE_DIR}/VHACD_Lib")

if(MASTER_PROJECT)
	add_subdirectory ("${CMAKE_CURRENT_SOURCE_DIR}/test")
endif()
