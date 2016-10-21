set(SUBSYS_NAME gpu_segmentation)
set(SUBSYS_PATH gpu/segmentation)
set(SUBSYS_DESC "Point cloud GPU segmentation library")
set(SUBSYS_DEPS common gpu_containers gpu_utils gpu_octree)

set(build TRUE)
PCL_SUBSYS_OPTION(build "${SUBSYS_NAME}" "${SUBSYS_DESC}" ON)
PCL_SUBSYS_DEPEND(build "${SUBSYS_NAME}" DEPS ${SUBSYS_DEPS})
mark_as_advanced("BUILD_${SUBSYS_NAME}")

PCL_ADD_DOC("${SUBSYS_NAME}")
