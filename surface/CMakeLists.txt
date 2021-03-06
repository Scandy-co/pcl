set(SUBSYS_NAME surface)
set(SUBSYS_DESC "Point cloud surface library")
set(SUBSYS_DEPS common search kdtree octree)

set(build TRUE)
PCL_SUBSYS_OPTION(build "${SUBSYS_NAME}" "${SUBSYS_DESC}" ON)
PCL_SUBSYS_DEPEND(build "${SUBSYS_NAME}" DEPS ${SUBSYS_DEPS} OPT_DEPS qhull)

PCL_ADD_DOC("${SUBSYS_NAME}")

if(build)
    if(QHULL_FOUND)
        set(HULL_INCLUDES
            "include/pcl/${SUBSYS_NAME}/concave_hull.h"
            "include/pcl/${SUBSYS_NAME}/convex_hull.h"
            "include/pcl/${SUBSYS_NAME}/qhull.h"
            )
        set(HULL_IMPLS
            "include/pcl/${SUBSYS_NAME}/impl/concave_hull.hpp"
            "include/pcl/${SUBSYS_NAME}/impl/convex_hull.hpp"
            )
        set(HULL_SOURCES
            src/concave_hull.cpp
            src/convex_hull.cpp
            )
    endif(QHULL_FOUND)

    if (VTK_FOUND AND NOT ANDROID)
        set(VTK_USE_FILE "${VTK_USE_FILE}" CACHE INTERNAL "VTK_USE_FILE")
        include("${VTK_USE_FILE}")

        set(VTK_SMOOTHING_INCLUDES
            "include/pcl/${SUBSYS_NAME}/vtk_smoothing/vtk.h"
            "include/pcl/${SUBSYS_NAME}/vtk_smoothing/vtk_utils.h"
            "include/pcl/${SUBSYS_NAME}/vtk_smoothing/vtk_mesh_subdivision.h"
            "include/pcl/${SUBSYS_NAME}/vtk_smoothing/vtk_mesh_quadric_decimation.h"
            "include/pcl/${SUBSYS_NAME}/vtk_smoothing/vtk_mesh_smoothing_laplacian.h"
            "include/pcl/${SUBSYS_NAME}/vtk_smoothing/vtk_mesh_smoothing_windowed_sinc.h")

        set(VTK_SMOOTHING_SOURCE
            src/vtk_smoothing/vtk_utils.cpp
            src/vtk_smoothing/vtk_mesh_subdivision.cpp
            src/vtk_smoothing/vtk_mesh_quadric_decimation.cpp
            src/vtk_smoothing/vtk_mesh_smoothing_laplacian.cpp
            src/vtk_smoothing/vtk_mesh_smoothing_windowed_sinc.cpp)

        if("${VTK_MAJOR_VERSION}.${VTK_MINOR_VERSION}" VERSION_LESS "6.0")
            set(VTK_SMOOTHING_TARGET_LINK_LIBRARIES vtkCommon vtkWidgets vtkGraphics)
        else("${VTK_MAJOR_VERSION}.${VTK_MINOR_VERSION}" VERSION_LESS "6.0")
            set(VTK_SMOOTHING_TARGET_LINK_LIBRARIES vtkCommonCore vtkCommonDataModel vtkCommonExecutionModel vtkFiltersModeling)
        endif("${VTK_MAJOR_VERSION}.${VTK_MINOR_VERSION}" VERSION_LESS "6.0")
    endif()

    SET(BUILD_surface_on_nurbs 0 CACHE BOOL "Fitting NURBS to point-clouds using openNURBS" )
    IF(BUILD_surface_on_nurbs)
        include(src/3rdparty/opennurbs/openNURBS.cmake)
        include(src/on_nurbs/on_nurbs.cmake)
    ENDIF(BUILD_surface_on_nurbs)

    set(POISSON_INCLUDES

        )
    set(POISSON_SOURCES
        )

    set(srcs
        src/processing.cpp
        src/ear_clipping.cpp
        src/organized_fast_mesh.cpp
        src/simplification_remove_unused_vertices.cpp
        src/surfel_smoothing.cpp
        src/texture_mapping.cpp
        ${VTK_SMOOTHING_SOURCE}
        ${HULL_SOURCES}
        ${POISSON_SOURCES}
        ${OPENNURBS_SOURCES}
        ${ON_NURBS_SOURCES}
        )

    set(incs
        "include/pcl/${SUBSYS_NAME}/boost.h"
        "include/pcl/${SUBSYS_NAME}/eigen.h"
        "include/pcl/${SUBSYS_NAME}/ear_clipping.h"
        "include/pcl/${SUBSYS_NAME}/organized_fast_mesh.h"
        "include/pcl/${SUBSYS_NAME}/reconstruction.h"
        "include/pcl/${SUBSYS_NAME}/processing.h"
        "include/pcl/${SUBSYS_NAME}/simplification_remove_unused_vertices.h"
        "include/pcl/${SUBSYS_NAME}/surfel_smoothing.h"
        "include/pcl/${SUBSYS_NAME}/texture_mapping.h"
        ${HULL_INCLUDES}
      #  ${VTK_SMOOTHING_INCLUDES}
      #  ${POISSON_INCLUDES}
      #  ${OPENNURBS_INCLUDES}
      #  ${ON_NURBS_INCLUDES}
        )

    set(impl_incs
        "include/pcl/${SUBSYS_NAME}/impl/organized_fast_mesh.hpp"
        "include/pcl/${SUBSYS_NAME}/impl/reconstruction.hpp"
        "include/pcl/${SUBSYS_NAME}/impl/processing.hpp"
        "include/pcl/${SUBSYS_NAME}/impl/surfel_smoothing.hpp"
        "include/pcl/${SUBSYS_NAME}/impl/texture_mapping.hpp"
        ${HULL_IMPLS}
        )

    set(LIB_NAME "pcl_${SUBSYS_NAME}")
    include_directories("${CMAKE_CURRENT_SOURCE_DIR}/include" ${VTK_INCLUDE_DIRS} "${CMAKE_CURRENT_SOURCE_DIR}")
    link_directories(${VTK_LIBRARY_DIRS})
    PCL_ADD_LIBRARY("${LIB_NAME}" "${SUBSYS_NAME}" ${srcs} ${incs} ${impl_incs} ${VTK_SMOOTHING_INCLUDES} ${POISSON_INCLUDES} ${OPENNURBS_INCLUDES} ${ON_NURBS_INCLUDES})
    target_link_libraries("${LIB_NAME}" pcl_common pcl_search pcl_kdtree pcl_octree ${VTK_LIBRARIES} ${ON_NURBS_LIBRARIES})
    if(QHULL_FOUND)
      target_link_libraries("${LIB_NAME}" ${QHULL_LIBRARIES})
    endif(QHULL_FOUND)
    PCL_MAKE_PKGCONFIG("${LIB_NAME}" "${SUBSYS_NAME}" "${SUBSYS_DESC}" "${SUBSYS_DEPS}" "" "" "" "")

    # Install include files
    PCL_ADD_INCLUDES("${SUBSYS_NAME}" "${SUBSYS_NAME}" ${incs})
    PCL_ADD_INCLUDES("${SUBSYS_NAME}" "${SUBSYS_NAME}/impl" ${impl_incs})

    if(BUILD_surface_on_nurbs)
      add_definitions(-DUNICODE -D_UNICODE)
      PCL_ADD_INCLUDES("${SUBSYS_NAME}" "${SUBSYS_NAME}/3rdparty/opennurbs" ${OPENNURBS_INCLUDES})
      PCL_ADD_INCLUDES("${SUBSYS_NAME}" "${SUBSYS_NAME}/on_nurbs" ${ON_NURBS_INCLUDES})
    endif(BUILD_surface_on_nurbs)

    if (VTK_FOUND AND NOT ANDROID)
      PCL_ADD_INCLUDES("${SUBSYS_NAME}" "${SUBSYS_NAME}/vtk_smoothing" ${VTK_SMOOTHING_INCLUDES})
    endif (VTK_FOUND AND NOT ANDROID)
    if(WIN32)
      target_link_libraries("${LIB_NAME}" Rpcrt4.lib)
    endif(WIN32)
endif(build)
