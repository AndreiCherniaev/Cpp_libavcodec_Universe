This is C++ project that using libavcodec (FFMpeg), build system is cmake. 
## How build
Tested on Ubuntu 22 x86_64.
Prepare and [build](https://trac.ffmpeg.org/wiki/CompilationGuide/Ubuntu#FFmpeg) FFMpeg
```bash
git clone https://github.com/AndreiCherniaev/Cpp_libavcodec_Universe.git && MyBaseDir=${PWD}/Cpp_libavcodec_Universe && cd Cpp_libavcodec_Universe
chmod +x FFMpeg_themself/build_FFMpeg.sh
FFMpeg_themself/build_FFMpeg.sh
```
Build steps:
```bash
echo ${MyBaseDir} #check path
cd ${MyBaseDir}
cmake -S myExample/src/ -B myExample/build-host/ --fresh
```

## Result
Problem I describe below is already fixed by commit fbc9dcb89ae5554f9852a54628b313f6998621e9  
File myExample/src/CMakeLists.txt contains
```cmake
cmake_path(SET pkgconfig_path "${CMAKE_CURRENT_LIST_DIR}/../../FFMpeg_themself/ffmpeg_build/lib/pkgconfig/" NORMALIZE)
```
If you use "With Environment Variables" case
```cmake
#set(CMAKE_PREFIX_PATH ${pkgconfig_path}) #makes "A required package was not found"
set(ENV{PKG_CONFIG_PATH} ${pkgconfig_path}) #ok
```
then you should get ok result like this
```bash
~/Cpp_libavcodec_Universe$ cmake -S myExample/src/ -B myExample/build-host/ --fresh
-- The CXX compiler identification is GNU 11.4.0
-- The C compiler identification is GNU 11.4.0
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Check for working CXX compiler: /usr/bin/c++ - skipped
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Check for working C compiler: /usr/bin/cc - skipped
-- Detecting C compile features
-- Detecting C compile features - done
I found /home/user/Cpp_libavcodec_Universe/FFMpeg_themself/ffmpeg_build/lib/pkgconfig/
-- Found PkgConfig: /usr/bin/pkg-config (found version "0.29.2") 
-- Checking for module 'libavcodec'
--   Found libavcodec, version 60.25.100
-- Configuring done (0.2s)
-- Generating done (0.0s)
-- Build files have been written to: /home/user/Cpp_libavcodec_Universe/myExample/build-host
```
But if you use "Without Environment Variables" case
```cmake
set(CMAKE_PREFIX_PATH ${pkgconfig_path}) #makes "A required package was not found"
#set(ENV{PKG_CONFIG_PATH} ${pkgconfig_path}) #ok
```
then you should get bad result like this
```bash
I found 
-- Found PkgConfig: /usr/bin/pkg-config (found version "0.29.2") 
-- Checking for module 'libavcodec'
--   No package 'libavcodec' found
CMake Error at /home/user/.local/lib/python3.10/site-packages/cmake/data/share/cmake-3.26/Modules/FindPkgConfig.cmake:607 (message):
  A required package was not found
Call Stack (most recent call first):
  /home/user/.local/lib/python3.10/site-packages/cmake/data/share/cmake-3.26/Modules/FindPkgConfig.cmake:829 (_pkg_check_modules_internal)
  CMakeLists.txt:19 (pkg_check_modules)
```
