project(VHACD_LIB CXX C)
include(${CMAKE_COMMON_INC})

set(CMAKE_CXX_STANDARD 11)

set(LIB_TYPE "STATIC" CACHE STRING "STATIC, SHARED or MODULE?")
message("[VHACD] \t LIB_TYPE " ${LIB_TYPE})
add_library(vhacd ${LIB_TYPE} ${PROJECT_CPP_FILES} ${PROJECT_C_FILES} ${PROJECT_INC_FILES} ${PROJECT_INL_FILES} ${PROJECT_CL_FILES})

if (NOT NO_OPENCL)
	#include(FindOpenCL OPTIONAL)
	find_package(OpenCL)
endif()

if (NOT NO_OPENMP)
	#include(FindOpenMP OPTIONAL)
	find_package(OpenMP)
endif()

if(OPENMP_FOUND)
    target_link_libraries(vhacd PRIVATE OpenMP::OpenMP_CXX)
endif()

if (OpenCL_FOUND)
    target_link_libraries(vhacd PRIVATE OpenCL::OpenCL)
    target_compile_definitions(vhacd PRIVATE -DOPENCL_FOUND=1 )
    target_compile_definitions(vhacd PRIVATE -DOPENCL_CL_FILES="${PROJECT_CL_FILES}" )
endif()

target_include_directories(vhacd PUBLIC
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/public/>)

target_include_directories(vhacd PRIVATE
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/inc/>
  $<INSTALL_INTERFACE:include/>  # <prefix>/include/mylib
  )


message("[VHACD] \t -> CMAKE_INSTALL_PREFIX " ${CMAKE_INSTALL_PREFIX})
install(TARGETS vhacd EXPORT vhacd-targets DESTINATION lib)
install(FILES ${PROJECT_INC_FILES} DESTINATION include)
install(FILES ${PROJECT_INL_FILES} DESTINATION include)


set(VHACD_LIB_VERSION 3.2.0)
include(CMakePackageConfigHelpers)
write_basic_package_version_file(
  "${CMAKE_CURRENT_BINARY_DIR}/vhacd/vhacd-config-version.cmake"
  VERSION ${VHACD_LIB_VERSION}
  COMPATIBILITY AnyNewerVersion
)

export(EXPORT vhacd-targets NAMESPACE ::
  FILE "${CMAKE_CURRENT_BINARY_DIR}/vhacd/vhacd-targets.cmake"
)

configure_file(cmake/vhacd-config.cmake
  "${CMAKE_CURRENT_BINARY_DIR}/vhacd/vhacd-config.cmake"
  COPYONLY
)

set(ConfigPackageLocation lib/cmake/vhacd)
install(EXPORT vhacd-targets
  FILE
    vhacd-targets.cmake
  DESTINATION
    ${ConfigPackageLocation}
	NAMESPACE ::
)
install(
  FILES
    "${CMAKE_CURRENT_BINARY_DIR}/vhacd/vhacd-config.cmake"
    "${CMAKE_CURRENT_BINARY_DIR}/vhacd/vhacd-config-version.cmake"
  DESTINATION
    ${ConfigPackageLocation}
  COMPONENT
    Devel
)

