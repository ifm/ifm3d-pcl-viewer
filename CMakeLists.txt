cmake_minimum_required(VERSION 3.5)
cmake_policy(SET CMP0048 NEW)
project(IFM3D_PCL_VIEWER VERSION 0.2.0 LANGUAGES CXX)
set(GIT_PROJECT_NAME "ifm3d-pcl-viewer")

# Make our cmake functions accessible
set(CMAKE_MODULE_PATH
    ${IFM3D_PCL_VIEWER_SOURCE_DIR}/cmake/modules
    ${CMAKE_MODULE_PATH})

# Force an out-of-source build of the code
include(MacroOutOfSourceBuild)
macro_ensure_out_of_source_build(
  "Please build ${PROJECT_NAME} out-of-source")

# Installation root
set(CPACK_SET_DESTDIR true)
set(CMAKE_INSTALL_PREFIX "/usr" CACHE STRING "CMake install prefix")
set(CPACK_INSTALL_PREFIX ${CMAKE_INSTALL_PREFIX})

# Installation paths
set(_lib lib CACHE STRING "Basename of the library-directory")
set(_bin bin CACHE STRING "Basename of the bin-directory")
set(_include include CACHE STRING "Basename of the include-directory")

# Global compiler flags
set(CMAKE_BUILD_TYPE Release) # Release or Debug
set(CMAKE_CXX_EXTENSIONS OFF) # OFF -> -std=c++14, ON -> -std=gnu++14
set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQUIRED true)

# Let's build some code...
add_subdirectory(src)

# Debian packaging
file(COPY
  ${IFM3D_PCL_VIEWER_SOURCE_DIR}/cmake/utils/ifm3d-pcl-viewer-dpkg-deps.py.in
  DESTINATION ${IFM3D_PCL_VIEWER_BINARY_DIR}
  FILE_PERMISSIONS OWNER_READ
                   OWNER_WRITE
                   OWNER_EXECUTE
                   GROUP_READ
                   GROUP_EXECUTE
                   WORLD_READ
                   WORLD_EXECUTE
  )
configure_file(
  ${IFM3D_PCL_VIEWER_BINARY_DIR}/ifm3d-pcl-viewer-dpkg-deps.py.in
  ${IFM3D_PCL_VIEWER_BINARY_DIR}/ifm3d-pcl-viewer-dpkg-deps.py
  @ONLY
  )

set(CPACK_GENERATOR "DEB")

# Package architecture
find_program(DPKG_CMD dpkg)
if(NOT CMAKE_CROSSCOMPILING)
  if(NOT DPKG_CMD)
    message(STATUS
      "Cannot find dpkg in your path, default to ${CMAKE_SYSTEM_PROCESSOR}.")
    set(CPACK_DEBIAN_PACKAGE_ARCHITECTURE "${CMAKE_SYSTEM_PROCESSOR}")
  else()
    execute_process(COMMAND "${DPKG_CMD}" --print-architecture
      OUTPUT_VARIABLE CPACK_DEBIAN_PACKAGE_ARCHITECTURE
      OUTPUT_STRIP_TRAILING_WHITESPACE
      )
  endif(NOT DPKG_CMD)
else()
  # big assumption here
  set(CPACK_DEBIAN_PACKAGE_ARCHITECTURE "armhf")
endif()

set(CPACK_DEBIAN_PACKAGE_SECTION Video)
set(CPACK_DEBIAN_PACKAGE_NAME ${GIT_PROJECT_NAME})
set(CPACK_PACKAGE_DESCRIPTION_SUMMARY "PCL-based Visualization GUI for ifm3d")
set(CPACK_PACKAGE_VENDOR "ifm robotics")
set(CPACK_PACKAGE_CONTACT "Tom Panzarella <tom.panzarella@ifm.com>")
set(CPACK_PACKAGE_VERSION_MAJOR ${IFM3D_PCL_VIEWER_VERSION_MAJOR})
set(CPACK_PACKAGE_VERSION_MINOR ${IFM3D_PCL_VIEWER_VERSION_MINOR})
set(CPACK_PACKAGE_VERSION_PATCH ${IFM3D_PCL_VIEWER_VERSION_PATCH})
set(CPACK_PACKAGE_FILE_NAME
    "${GIT_PROJECT_NAME}_${IFM3D_PCL_VIEWER_VERSION}_${CPACK_DEBIAN_PACKAGE_ARCHITECTURE}")

# dynamically create dependencies of the various modules
#set(CPACK_DEBIAN_PACKAGE_SHLIBDEPS ON) # <-- Never works correctly
add_custom_target(repackage
  COMMAND ${IFM3D_PCL_VIEWER_BINARY_DIR}/ifm3d-pcl-viewer-dpkg-deps.py ${IFM3D_PCL_VIEWER_BINARY_DIR}/*.deb
  )

include(CPack)
