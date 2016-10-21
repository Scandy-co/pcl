set(SUBSYS_NAME gpu_kinfu_large_scale)
set(SUBSYS_PATH gpu/kinfu_large_scale)
set(SUBSYS_DESC "Kinect Fusion implementation, with volume shifting")
set(SUBSYS_DEPS common visualization io gpu_containers gpu_utils geometry search octree filters kdtree features surface)

set(build FALSE)

# OpenNI found?
if(NOT WITH_OPENNI)
    set(DEFAULT FALSE)
    set(REASON "OpenNI was not found or was disabled by the user.")
else()
    set(DEFAULT TRUE)
    set(REASON)
endif()

PCL_SUBSYS_OPTION(build "${SUBSYS_NAME}" "${SUBSYS_DESC}" ${DEFAULT} "${REASON}")
PCL_SUBSYS_DEPEND(build "${SUBSYS_NAME}" DEPS ${SUBSYS_DEPS})
PCL_SET_SUBSYS_INCLUDE_DIR("${SUBSYS_NAME}" "${SUBSYS_PATH}")
mark_as_advanced("BUILD_${SUBSYS_NAME}")

if (build)
	REMOVE_VTK_DEFINITIONS()
	FILE(GLOB incs include/pcl/gpu/kinfu_large_scale/*.h*)
	FILE(GLOB impl_incs include/pcl/gpu/kinfu_large_scale/impl/*.h*)
	FILE(GLOB srcs src/*.cpp src/*.h*)


	source_group("Source Files" FILES ${srcs} )

	set(LIB_NAME "pcl_${SUBSYS_NAME}")
	include_directories("${CMAKE_CURRENT_SOURCE_DIR}/include" "${CMAKE_CURRENT_SOURCE_DIR}/src")

	PCL_ADD_LIBRARY("${LIB_NAME}" "${SUBSYS_NAME}" ${srcs} ${incs} ${impl_incs})

	set(EXT_DEPS "")
	PCL_MAKE_PKGCONFIG("${LIB_NAME}" "${SUBSYS_NAME}" "${SUBSYS_DESC}" "${SUBSYS_DEPS}" "${EXT_DEPS}" "" "" "")

	# Install include files
	PCL_ADD_INCLUDES("${SUBSYS_NAME}" "${SUBSYS_PATH}" ${incs})

	add_subdirectory(test)
	add_subdirectory(tools)
endif()
