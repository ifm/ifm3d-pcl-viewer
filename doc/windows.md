

# How to build ifm3d-pcl-viewer from source on Windows.

- [How to build ifm3d-pcl-viewer from source on Windows.](#how-to-build-ifm3d-pcl-viewer-from-source-on-windows)
- [Dependencies](#Dependencies)
    - [ifm3d library](#ifm3d-library)
- [Building](#Building)
- [Running ifm3d-pcl-viewer on Windows.](#running-ifm3d-pcl-viewer-on-windows)


# Dependencies
## ifm3d library

Make sure that you have completed the steps necessary for installing ifm3d on Windows

* [ifm3d library on windows](https://github.com/lovepark/ifm3d/blob/master/doc/windows.md)

These instructions will install all of the dependencies required for ifm3d-pcl-viewer.

Note: When building ifm3d, -DBUILD_SDK_PKG must be set to ON in the first cmake step. If you have previously built ifm3d with it OFF, you must rebuild ifm3d.

Note: These instructions assume all environment variables have been set according to the ifm3d instructions.


# Building 
Download:
```
cd %IFM3D_BUILD_DIR%
git clone  https://github.com/lovepark/ifm3d-pcl-viewer.git
```

Build
```
cd %IFM3D_BUILD_DIR%\ifm3d-pcl-viewer
mkdir build
cd build
cmake -Ax64 -DCMAKE_WINDOWS_EXPORT_ALL_SYMBOLS=ON -DCMAKE_PREFIX_PATH="%IFM3D_BUILD_DIR%\install" -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=%IFM3D_BUILD_DIR%\install ..
cmake --build . --clean-first --config Release --target INSTALL
```

# Running ifm3d-pcl-viewer on Windows
After Building ifm3d-pcl-viewer, the binary will be installed at ``%IFM3D_BUILD_DIR%\install\bin``. To run the ifm3d-pcl-viewer you need to add this directory to your path. You will also need to add the opencv and boost binary directories to your ``PATH``. This step should have been completed as part of the ifm3d library setup.

```
ifm3d-pcl-viewer
```
![viewer](figures/ifm3d_viewer.png)

