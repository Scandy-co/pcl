set(SUBSYS_NAME features)
set(SUBSYS_DESC "Point cloud features library")
set(SUBSYS_DEPS common search kdtree octree filters 2d)

set(build TRUE)
PCL_SUBSYS_OPTION(build "${SUBSYS_NAME}" "${SUBSYS_DESC}" ON)
PCL_SUBSYS_DEPEND(build "${SUBSYS_NAME}" DEPS ${SUBSYS_DEPS})

PCL_ADD_DOC("${SUBSYS_NAME}")

if(build)
    include_directories("${CMAKE_CURRENT_SOURCE_DIR}/include")

    set(incs
        "include/pcl/${SUBSYS_NAME}/boost.h"
        "include/pcl/${SUBSYS_NAME}/eigen.h"
        "include/pcl/${SUBSYS_NAME}/feature.h"
        "include/pcl/${SUBSYS_NAME}/normal_3d.h"
        )

    set(impl_incs
        "include/pcl/${SUBSYS_NAME}/impl/feature.hpp"
        "include/pcl/${SUBSYS_NAME}/impl/normal_3d.hpp"
        )

    set(srcs
        src/normal_3d.cpp
       )

    if(MSVC)
      # Workaround to aviod hitting the MSVC 4GB linker memory limit when building pcl_features.
      # Disable whole program optimization (/GL) and link-time code generation (/LTCG).
      string(REPLACE "/GL" "" CMAKE_CXX_FLAGS_RELEASE ${CMAKE_CXX_FLAGS_RELEASE})
      string(REPLACE "/LTCG" "" CMAKE_SHARED_LINKER_FLAGS_RELEASE ${CMAKE_SHARED_LINKER_FLAGS_RELEASE})
    endif(MSVC)

    set(LIB_NAME "pcl_${SUBSYS_NAME}")
    PCL_ADD_LIBRARY("${LIB_NAME}" "${SUBSYS_NAME}" ${srcs} ${incs} ${impl_incs})
    target_link_libraries("${LIB_NAME}" pcl_common pcl_search pcl_kdtree pcl_octree)
    PCL_MAKE_PKGCONFIG("${LIB_NAME}" "${SUBSYS_NAME}" "${SUBSYS_DESC}" "${SUBSYS_DEPS}" "" "" "" "")
    # Install headers
    PCL_ADD_INCLUDES("${SUBSYS_NAME}" "${SUBSYS_NAME}" ${incs})
    PCL_ADD_INCLUDES("${SUBSYS_NAME}" "${SUBSYS_NAME}/impl" ${impl_incs})

endif(build)
