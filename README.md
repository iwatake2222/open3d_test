# Try Demo Program (Linux only)
```sh
# pip install open3d
wget https://github.com/isl-org/Open3D/releases/download/v0.14.1/open3d-viewer-0.14.1-Linux.deb
apt install ./open3d-viewer-0.14.1-Linux.deb
wget https://raw.githubusercontent.com/McNopper/OpenGL/master/Binaries/teapot.obj
Open3D teapot.obj
```
# Install Open3D in Python
```sh
pip install open3d
```

# Install Open3D in C++
## Linux
```sh
apt update
apt isntall -y wget build-essential curl git nano
wget -O - https://apt.kitware.com/keys/kitware-archive-latest.asc 2>/dev/null | gpg --dearmor - |  tee /usr/share/keyrings/kitware-archive-keyring.gpg >/dev/null
echo 'deb [signed-by=/usr/share/keyrings/kitware-archive-keyring.gpg] https://apt.kitware.com/ubuntu/ focal main' |  tee /etc/apt/sources.list.d/kitware.list >/dev/null
apt update
# apt-key adv --keyserver keyserver.ubuntu.com --recv-keys  DE19EB17684BA42D
apt install -y cmake

git clone https://github.com/isl-org/Open3D
cd Open3D
nano util/install_deps_ubuntu.sh
# Dockerの場合は、sudoをblankにする
util/install_deps_ubuntu.sh

mkdir build
cd build
cmake .. -DBUILD_PYTHON_MODULE=OFF
make -j$(nproc)
make install

ls /usr/local/include/open3d/
ls /usr/local/lib/libOpen3D.a
ls /usr/local/lib/cmake/Open3D/Open3DTargets.cmake
ls /usr/local/lib/cmake/Open3D/Open3DTargets-release.cmake
```

## Windows
- `git clone https://github.com/isl-org/Open3D`
- Modify cmake to ignore warning while building in Visual Studio
    - Add `/wd4819` to `Open3DShowAndAbortOnWarning.cmake`

```
diff --git a/cmake/Open3DShowAndAbortOnWarning.cmake b/cmake/Open3DShowAndAbortOnWarning.cmake
index dbfee1c95..767b921e7 100644
--- a/cmake/Open3DShowAndAbortOnWarning.cmake
+++ b/cmake/Open3DShowAndAbortOnWarning.cmake
@@ -19,6 +19,7 @@ function(open3d_show_and_abort_on_warning target)
         /wd4245        # signed/unsigned mismatch (visualization, PoissonRecon, ...)
         /wd4267        # conversion from size_t to smaller type (FixedRadiusSearchCUDA, tests)
         /wd4305        # conversion to smaller type in initialization or constructor argument (examples, tests)
+        /wd4819
     )
     set(DISABLE_GNU_CLANG_INTEL_WARNINGS
         -Wno-unused-parameter               # (many places in Open3D code)
```

- Generate project using cmake
    - BUILD_PYTHON_MODULE = OFF
    - BUILD_SHARED_LIBS = ON , STATIC_WINDOWS_RUNTIME = OFF, BUILD_WEBRTC = OFF
        - otherwise, you'll need to build Open3D static libraries for all configurations (Release, Debug, RelWithDeb, etc.)
        - note: `STATIC_WINDOWS_RUNTIME = OFF` sets runtime library to MD rather than MT. So that you can use the DLL built in Release mode when debugging your app in Debug mode
    - CMAKE_INSTALL_PREFIX=./install
- Open the generated project in isual Studio 2019
    - Build    (in Release mode)
    - Build "install" (CMakePredefinedTargets -> INSTALL)


## Official Sample Project
### Linux
```sh
git clone https://github.com/intel-isl/open3d-cmake-find-package.git
cd open3d-cmake-find-package
mkdir build
cd build
cmake  ..
make
./Draw
```

### Windows
- `git clone https://github.com/intel-isl/open3d-cmake-find-package.git`
- Generate project using cmake
    - Open3D_DIR = {path-to-build-Open3D}/install/Open3D/CMake
        - e.g.: `C:/iwatake/devel/build/Open3D/install/CMake`
        - note: use `/` rathar than `\`
    - CMAKE_INSTALL_PREFIX = {path-to-build-Open3D}/install
        - you need this, if you have built the Open3D as DLL(BUILD_SHARED_LIBS = ON).  because cmake in the sample project tries to copy DLL file
- After opening the generated project, change `Runtime Library` in Debug/Release mode to `Multi-threaded DLL(/MD)`
    - you need this, if you have built the Open3D as DLL(BUILD_SHARED_LIBS = ON) and you want to debug your app linking the DLL


