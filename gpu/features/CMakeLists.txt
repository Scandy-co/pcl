set(SUBSYS_NAME gpu_features)
set(SUBSYS_PATH gpu/features)
set(SUBSYS_DESC "Temporary GPU_common module. Weak CUDA dependency: a code that use this module requires CUDA Toolkit installed, but should be compiled without nvcc")
set(SUBSYS_DEPS common gpu_containers gpu_utils gpu_octree geometry)

set(build TRUE)
PCL_SUBSYS_OPTION(build "${SUBSYS_NAME}" "${SUBSYS_DESC}" ON)
PCL_SUBSYS_DEPEND(build "${SUBSYS_NAME}" DEPS ${SUBSYS_DEPS})
PCL_SET_SUBSYS_INCLUDE_DIR("${SUBSYS_NAME}" "${SUBSYS_PATH}")
mark_as_advanced("BUILD_${SUBSYS_NAME}")
