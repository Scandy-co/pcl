set(SUBSYS_NAME sample_consensus)
set(SUBSYS_DESC "Point cloud sample consensus library")
set(SUBSYS_DEPS common search)

set(build TRUE)
PCL_SUBSYS_OPTION(build "${SUBSYS_NAME}" "${SUBSYS_DESC}" ON)
PCL_SUBSYS_DEPEND(build "${SUBSYS_NAME}" DEPS ${SUBSYS_DEPS})

PCL_ADD_DOC("${SUBSYS_NAME}")
