find_package(ifm3d 0.9.0 CONFIG REQUIRED COMPONENTS camera framegrabber image)
find_package(PCL 1.7 REQUIRED COMPONENTS visualization)
#
# This is some legacy nonsense we need to deal with, see:
# https://bugs.launchpad.net/ubuntu/+source/vtk6/+bug/1573234
#
list(FIND PCL_VISUALIZATION_LIBRARIES "vtkproj4" VTKPROJ4_REQUIRED)
if(NOT VTKPROJ4_REQUIRED EQUAL -1)
  find_library(LIBVTKPROJ4_LIBRARY NAMES vtkproj4)
  if(NOT ${LIBVTKPROJ4_LIBRARY})
    list(REMOVE_ITEM PCL_VISUALIZATION_LIBRARIES "vtkproj4")
  endif()
endif()

add_executable(ifm3d-pcl-viewer ifm3d-pcl-viewer.cpp)
target_link_libraries(ifm3d-pcl-viewer
                      ifm3d::camera
                      ifm3d::framegrabber
                      ifm3d::image
                      ${PCL_VISUALIZATION_LIBRARIES}
                      )

install(TARGETS ifm3d-pcl-viewer DESTINATION ${_bin})
