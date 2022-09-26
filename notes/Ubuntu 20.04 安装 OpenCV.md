---
title: Ubuntu 20.04 安装 OpenCV
tags: [Video Audio Effects]
created: 2022-09-26T06:00:09.217Z
modified: 2022-09-26T12:50:01.460Z
---

# Ubuntu 20.04 安装 OpenCV

## 1. 方式一：使用 Ubuntu 仓库安装 OpenCV

```shell
$ sudo apt update

$ sudo apt install libopencv-dev python3-opencv
```

验证版本：

```
$ opencv_version
4.2.0
```

## 2. 方式二：通过源码编译安装 OpenCV

### 2.1. 安装构建工具和依赖库

使用以下命令来安装构建工具和依赖包：

```shell
$ sudo apt install build-essential cmake git pkg-config libgtk-3-dev \
libavcodec-dev libavformat-dev libswscale-dev libv4l-dev \
libxvidcore-dev libx264-dev libjpeg-dev libpng-dev libtiff-dev \
gfortran openexr libatlas-base-dev python3-dev python3-numpy \
libtbb2 libtbb-dev libdc1394-22-dev libopenexr-dev \
libgstreamer-plugins-base1.0-dev libgstreamer1.0-dev
```

### 2.2. 克隆 OpenCV 代码仓库

克隆 OpenCV 仓库：

```shell
$ mkdir ~/opencv_build && cd ~/opencv_build

$ git clone https://github.com/opencv/opencv.git
```

克隆 OpenCV 软件仓库：

```shell
$ git clone https://github.com/opencv/opencv_contrib.git
```

### 2.3. 配置与构建 OpenCV

创建构建目录：

```shell
$ cd ~/opencv_build/opencv

$ mkdir build && cd build
```

开始构建：

```shell
$ cmake -D CMAKE_BUILD_TYPE=RELEASE \
-D CMAKE_INSTALL_PREFIX=/usr/local \
-D INSTALL_C_EXAMPLES=ON \
-D INSTALL_PYTHON_EXAMPLES=ON \
-D OPENCV_GENERATE_PKGCONFIG=ON \
-D OPENCV_EXTRA_MODULES_PATH=~/opencv_build/opencv_contrib/modules \
-D BUILD_EXAMPLES=ON ..
```

### 2.4. 编译安装 OpenCV

```shell
$ make j8

$ sudo make install
```

### 2.5. 验证版本

```shell
$ pkg-config --modversion opencv4
4.6.0-dev
```

### 2.6. 卸载 OpenCV

```shell
$ cd ~/opencv_build/opencv/build

$ sudo make uninstall
```

## 3. 扩展一：使用 CUDA 11.6 和 CUDNN 8.4 安装 OpenCV 4.2.0

### 3.1. 安装构建工具与依赖库

- 通用工具：

  ```shell
  $ sudo apt install build-essential cmake pkg-config unzip yasm git checkinstall
  ```

- 图像 I/O 库：

  ```shell
  $ sudo apt install libjpeg-dev libpng-dev libtiff-dev
  ```

- 音视频库 - FFMPEG、GSTREAMER、X264 等等：

  ```shell
  $ sudo apt install libavcodec-dev libavformat-dev libswscale-dev libavresample-dev
  $ sudo apt install libgstreamer1.0-dev libgstreamer-plugins-base1.0-dev
  $ sudo apt install libxvidcore-dev x264 libx264-dev libfaac-dev libmp3lame-dev libtheora-dev
  $ sudo apt install libfaac-dev libmp3lame-dev libvorbis-dev
  ```

- OpenCore - 自适应多速率窄带 (AMRNB) 和宽带 (AMRWB) 语音编解码器：

  ```shell
  $ sudo apt install libopencore-amrnb-dev libopencore-amrwb-dev
  ```

- 摄像头编程接口库：

  ```shell
  $ sudo apt-get install libdc1394-22 libdc1394-22-dev libxine2-dev libv4l-dev v4l-utils
  $ cd /usr/include/linux
  $ sudo ln -s -f ../libv4l1-videodev.h videodev.h
  $ cd ~
  ```

- OpenCV highghui 模块的图形用户功能的 GTK 库：

  ```shell
  $ sudo apt-get install libgtk-3-dev
  ```

- Python 3 的 Python 库：

  ```shell
  $ sudo apt-get install python3-dev python3-pip
  $ sudo -H pip3 install -U pip numpy
  $ sudo apt install python3-testresources
  ```

- C++ CPU 并发库：

  ```shell
  $ sudo apt-get install libtbb-dev
  ```

- OpenCV 优化库：

  ```shell
  $ sudo apt-get install libatlas-base-dev gfortran
  ```

- 可选库：

  ```shell
  $ sudo apt-get install libprotobuf-dev protobuf-compiler
  $ sudo apt-get install libgoogle-glog-dev libgflags-dev
  $ sudo apt-get install libgphoto2-dev libeigen3-dev libhdf5-dev doxygen
  ```

### 3.2. 下载指定版本 OpenCV 与 OpenCV Contrib

```shell
$ mkdir ~/opencv_build && cd ~/opencv_build

$ wget -O opencv-4.6.0.tar.gz https://github.com/opencv/opencv/archive/refs/tags/4.6.0.tar.gz
$ wget -O opencv_contrib-4.6.0.tar.gz https://github.com/opencv/opencv_contrib/archive/refs/tags/4.6.0.tar.gz

$ tar zxf opencv-4.6.0.tar.gz
$ tar zxf opencv_contrib-4.6.0.tar.gz
```

### 3.3. 配置与构建 OpenCV

创建构建目录：

```shell
$ cd opencv-4.6.0
$ mkdir build && cd build
```

开始构建：

```shell
$ cmake -D CMAKE_BUILD_TYPE=RELEASE \
-D CMAKE_INSTALL_PREFIX=/usr/local \
-D WITH_TBB=ON \
-D ENABLE_FAST_MATH=1 \
-D CUDA_FAST_MATH=1 \
-D WITH_CUBLAS=1 \
-D WITH_CUDA=ON \
-D BUILD_opencv_cudacodec=OFF \
-D WITH_CUDNN=ON \
-D OPENCV_DNN_CUDA=ON \
-D CUDA_ARCH_BIN=8.6 \
-D WITH_V4L=ON \
-D WITH_QT=OFF \
-D WITH_OPENGL=ON \
-D WITH_GSTREAMER=ON \
-D OPENCV_GENERATE_PKGCONFIG=ON \
-D OPENCV_PC_FILE_NAME=opencv.pc \
-D OPENCV_ENABLE_NONFREE=ON \
-D OPENCV_PYTHON3_INSTALL_PATH=/usr/lib/python3/dist-packages \
-D PYTHON_EXECUTABLE=/usr/bin/python3 \
-D OPENCV_EXTRA_MODULES_PATH=~/opencv_build/opencv_contrib-4.6.0/modules \
-D INSTALL_PYTHON_EXAMPLES=OFF \
-D INSTALL_C_EXAMPLES=OFF \
-D BUILD_EXAMPLES=OFF ..
```

一些选项说明如下：

- 如果需要构建静态库，需要添加配置 `-D BUILD_SHARED_LIBS=OFF`

- 如果需要使用 CUDA，需要添加配置 ` -D WITH_CUDA=ON`

- 如果需要使用 CUDNN，则需要包含以下配置（需要设置正确的 CUDA_ARCH_BIN 值，可以访问 https://developer.nvidia.com/cuda-gpus 来查询；还可以参考[附录一：Ubuntu 20.04 查看 CUDA 的 CUDA_ARCH_BIN](#51-附录一ubuntu-2004-查看-cuda-的-cudaarchbin)）：

  ```shell
  -D WITH_CUDNN=ON \
  -D OPENCV_DNN_CUDA=ON \
  -D CUDA_ARCH_BIN=8.6 \
  ```

构建完成生成的配置信息如下：

```subrip-text
--
-- General configuration for OpenCV 4.6.0 =====================================
--   Version control:               unknown
--
--   Extra modules:
--     Location (extra):            /home/developer/opencv_build/opencv_contrib-4.6.0/modules
--     Version control (extra):     unknown
--
--   Platform:
--     Timestamp:                   2022-09-26T10:22:36Z
--     Host:                        Linux 5.15.0-48-generic x86_64
--     CMake:                       3.16.3
--     CMake generator:             Unix Makefiles
--     CMake build tool:            /usr/bin/make
--     Configuration:               RELEASE
--
--   CPU/HW features:
--     Baseline:                    SSE SSE2 SSE3
--       requested:                 SSE3
--     Dispatched code generation:  SSE4_1 SSE4_2 FP16 AVX AVX2 AVX512_SKX
--       requested:                 SSE4_1 SSE4_2 AVX FP16 AVX2 AVX512_SKX
--       SSE4_1 (18 files):         + SSSE3 SSE4_1
--       SSE4_2 (2 files):          + SSSE3 SSE4_1 POPCNT SSE4_2
--       FP16 (1 files):            + SSSE3 SSE4_1 POPCNT SSE4_2 FP16 AVX
--       AVX (5 files):             + SSSE3 SSE4_1 POPCNT SSE4_2 AVX
--       AVX2 (33 files):           + SSSE3 SSE4_1 POPCNT SSE4_2 FP16 FMA3 AVX AVX2
--       AVX512_SKX (8 files):      + SSSE3 SSE4_1 POPCNT SSE4_2 FP16 FMA3 AVX AVX2 AVX_512F AVX512_COMMON AVX512_SKX
--
--   C/C++:
--     Built as dynamic libs?:      YES
--     C++ standard:                11
--     C++ Compiler:                /usr/bin/c++  (ver 9.4.0)
--     C++ flags (Release):         -fsigned-char -ffast-math -W -Wall -Wreturn-type -Wnon-virtual-dtor -Waddress -Wsequence-point -Wformat -Wformat-security -Wmissing-declarations -Wundef -Winit-self -Wpointer-arith -Wshadow -Wsign-promo -Wuninitialized -Wsuggest-override -Wno-delete-non-virtual-dtor -Wno-comment -Wimplicit-fallthrough=3 -Wno-strict-overflow -fdiagnostics-show-option -Wno-long-long -pthread -fomit-frame-pointer -ffunction-sections -fdata-sections  -msse -msse2 -msse3 -fvisibility=hidden -fvisibility-inlines-hidden -O3 -DNDEBUG  -DNDEBUG
--     C++ flags (Debug):           -fsigned-char -ffast-math -W -Wall -Wreturn-type -Wnon-virtual-dtor -Waddress -Wsequence-point -Wformat -Wformat-security -Wmissing-declarations -Wundef -Winit-self -Wpointer-arith -Wshadow -Wsign-promo -Wuninitialized -Wsuggest-override -Wno-delete-non-virtual-dtor -Wno-comment -Wimplicit-fallthrough=3 -Wno-strict-overflow -fdiagnostics-show-option -Wno-long-long -pthread -fomit-frame-pointer -ffunction-sections -fdata-sections  -msse -msse2 -msse3 -fvisibility=hidden -fvisibility-inlines-hidden -g  -O0 -DDEBUG -D_DEBUG
--     C Compiler:                  /usr/bin/cc
--     C flags (Release):           -fsigned-char -ffast-math -W -Wall -Wreturn-type -Waddress -Wsequence-point -Wformat -Wformat-security -Wmissing-declarations -Wmissing-prototypes -Wstrict-prototypes -Wundef -Winit-self -Wpointer-arith -Wshadow -Wuninitialized -Wno-comment -Wimplicit-fallthrough=3 -Wno-strict-overflow -fdiagnostics-show-option -Wno-long-long -pthread -fomit-frame-pointer -ffunction-sections -fdata-sections  -msse -msse2 -msse3 -fvisibility=hidden -O3 -DNDEBUG  -DNDEBUG
--     C flags (Debug):             -fsigned-char -ffast-math -W -Wall -Wreturn-type -Waddress -Wsequence-point -Wformat -Wformat-security -Wmissing-declarations -Wmissing-prototypes -Wstrict-prototypes -Wundef -Winit-self -Wpointer-arith -Wshadow -Wuninitialized -Wno-comment -Wimplicit-fallthrough=3 -Wno-strict-overflow -fdiagnostics-show-option -Wno-long-long -pthread -fomit-frame-pointer -ffunction-sections -fdata-sections  -msse -msse2 -msse3 -fvisibility=hidden -g  -O0 -DDEBUG -D_DEBUG
--     Linker flags (Release):      -Wl,--exclude-libs,libippicv.a -Wl,--exclude-libs,libippiw.a   -Wl,--gc-sections -Wl,--as-needed -Wl,--no-undefined
--     Linker flags (Debug):        -Wl,--exclude-libs,libippicv.a -Wl,--exclude-libs,libippiw.a   -Wl,--gc-sections -Wl,--as-needed -Wl,--no-undefined
--     ccache:                      NO
--     Precompiled headers:         NO
--     Extra dependencies:          m pthread cudart_static dl rt nppc nppial nppicc nppidei nppif nppig nppim nppist nppisu nppitc npps cublas cudnn cufft -L/usr/local/cuda-11.6/lib64 -L/usr/lib/x86_64-linux-gnu
--     3rdparty dependencies:
--
--   OpenCV modules:
--     To be built:                 alphamat aruco barcode bgsegm bioinspired calib3d ccalib core cudaarithm cudabgsegm cudafeatures2d cudafilters cudaimgproc cudalegacy cudaobjdetect cudaoptflow cudastereo cudawarping cudev datasets dnn dnn_objdetect dnn_superres dpm face features2d flann freetype fuzzy gapi hdf hfs highgui img_hash imgcodecs imgproc intensity_transform line_descriptor mcc ml objdetect optflow phase_unwrapping photo plot python3 quality rapid reg rgbd saliency sfm shape stereo stitching structured_light superres surface_matching text tracking ts video videoio videostab wechat_qrcode xfeatures2d ximgproc xobjdetect xphoto
--     Disabled:                    cudacodec world
--     Disabled by dependency:      -
--     Unavailable:                 cvv java julia matlab ovis python2 viz
--     Applications:                tests perf_tests apps
--     Documentation:               NO
--     Non-free algorithms:         YES
--
--   GUI:                           GTK3
--     GTK+:                        YES (ver 3.24.20)
--       GThread :                  YES (ver 2.64.6)
--       GtkGlExt:                  NO
--     OpenGL support:              NO
--     VTK support:                 NO
--
--   Media I/O:
--     ZLib:                        /usr/lib/x86_64-linux-gnu/libz.so (ver 1.2.11)
--     JPEG:                        /usr/lib/x86_64-linux-gnu/libjpeg.so (ver 80)
--     WEBP:                        build (ver encoder: 0x020f)
--     PNG:                         /usr/lib/x86_64-linux-gnu/libpng.so (ver 1.6.37)
--     TIFF:                        /usr/lib/x86_64-linux-gnu/libtiff.so (ver 42 / 4.1.0)
--     JPEG 2000:                   build (ver 2.4.0)
--     OpenEXR:                     /usr/lib/x86_64-linux-gnu/libImath.so /usr/lib/x86_64-linux-gnu/libIlmImf.so /usr/lib/x86_64-linux-gnu/libIex.so /usr/lib/x86_64-linux-gnu/libHalf.so /usr/lib/x86_64-linux-gnu/libIlmThread.so (ver 2_3)
--     HDR:                         YES
--     SUNRASTER:                   YES
--     PXM:                         YES
--     PFM:                         YES
--
--   Video I/O:
--     DC1394:                      YES (2.2.5)
--     FFMPEG:                      YES
--       avcodec:                   YES (58.54.100)
--       avformat:                  YES (58.29.100)
--       avutil:                    YES (56.31.100)
--       swscale:                   YES (5.5.100)
--       avresample:                YES (4.0.0)
--     GStreamer:                   YES (1.16.3)
--     v4l/v4l2:                    YES (linux/videodev2.h)
--
--   Parallel framework:            TBB (ver 2020.1 interface 11101)
--
--   Trace:                         YES (with Intel ITT)
--
--   Other third-party libraries:
--     Intel IPP:                   2020.0.0 Gold [2020.0.0]
--            at:                   /home/developer/opencv_build/opencv-4.6.0/build/3rdparty/ippicv/ippicv_lnx/icv
--     Intel IPP IW:                sources (2020.0.0)
--               at:                /home/developer/opencv_build/opencv-4.6.0/build/3rdparty/ippicv/ippicv_lnx/iw
--     VA:                          NO
--     Lapack:                      NO
--     Eigen:                       YES (ver 3.3.7)
--     Custom HAL:                  NO
--     Protobuf:                    build (3.19.1)
--
--   NVIDIA CUDA:                   YES (ver 11.6, CUFFT CUBLAS FAST_MATH)
--     NVIDIA GPU arch:             86
--     NVIDIA PTX archs:
--
--   cuDNN:                         YES (ver 8.4.0)
--
--   OpenCL:                        YES (no extra features)
--     Include path:                /home/developer/opencv_build/opencv-4.6.0/3rdparty/include/opencl/1.2
--     Link libraries:              Dynamic load
--
--   Python 3:
--     Interpreter:                 /usr/bin/python3 (ver 3.8.10)
--     Libraries:                   /usr/lib/x86_64-linux-gnu/libpython3.8.so (ver 3.8.10)
--     numpy:                       /usr/local/lib/python3.8/dist-packages/numpy/core/include (ver 1.23.3)
--     install path:                /usr/lib/python3/dist-packages/cv2/python-3.8
--
--   Python (for build):            /usr/bin/python3
--
--   Java:
--     ant:                         NO
--     JNI:                         NO
--     Java wrappers:               NO
--     Java tests:                  NO
--
--   Install to:                    /usr/local
-- -----------------------------------------------------------------
--
-- Configuring done
-- Generating done
-- Build files have been written to: /home/developer/opencv_build/opencv-4.6.0/build
```

### 3.4. 编译安装 OpenCV

```shell
# 可以使用 make -j$(nproc) 来代替
$ make -j8

$ sudo make install
```

### 3.5. 验证版本

```shell
$ opencv_version
4.6.0
```

## 4. 问题记录

## 5. 附录

### 5.1. 附录一：Ubuntu 20.04 查看 CUDA 的 CUDA_ARCH_BIN

CUDA 11.6 以后不再提供 CUDA Samples，需要自己克隆仓库：

```shell
$ cd ~
$ git clone https://github.com/nvidia/cuda-samples

$ cd cuda-samples/Samples/1_Utilities/deviceQuery
$ sudo make

$ ./deviceQuery
```

输出内容如下：

```subrip-text
./deviceQuery Starting...

 CUDA Device Query (Runtime API) version (CUDART static linking)

Detected 1 CUDA Capable device(s)

Device 0: "NVIDIA GeForce RTX 3070"
  CUDA Driver Version / Runtime Version          11.6 / 11.6
  CUDA Capability Major/Minor version number:    8.6
  Total amount of global memory:                 7974 MBytes (8361213952 bytes)
  (046) Multiprocessors, (128) CUDA Cores/MP:    5888 CUDA Cores
  GPU Max Clock rate:                            1815 MHz (1.81 GHz)
  Memory Clock rate:                             7001 Mhz
  Memory Bus Width:                              256-bit
  L2 Cache Size:                                 4194304 bytes
  Maximum Texture Dimension Size (x,y,z)         1D=(131072), 2D=(131072, 65536), 3D=(16384, 16384, 16384)
  Maximum Layered 1D Texture Size, (num) layers  1D=(32768), 2048 layers
  Maximum Layered 2D Texture Size, (num) layers  2D=(32768, 32768), 2048 layers
  Total amount of constant memory:               65536 bytes
  Total amount of shared memory per block:       49152 bytes
  Total shared memory per multiprocessor:        102400 bytes
  Total number of registers available per block: 65536
  Warp size:                                     32
  Maximum number of threads per multiprocessor:  1536
  Maximum number of threads per block:           1024
  Max dimension size of a thread block (x,y,z): (1024, 1024, 64)
  Max dimension size of a grid size    (x,y,z): (2147483647, 65535, 65535)
  Maximum memory pitch:                          2147483647 bytes
  Texture alignment:                             512 bytes
  Concurrent copy and kernel execution:          Yes with 2 copy engine(s)
  Run time limit on kernels:                     Yes
  Integrated GPU sharing Host Memory:            No
  Support host page-locked memory mapping:       Yes
  Alignment requirement for Surfaces:            Yes
  Device has ECC support:                        Disabled
  Device supports Unified Addressing (UVA):      Yes
  Device supports Managed Memory:                Yes
  Device supports Compute Preemption:            Yes
  Supports Cooperative Kernel Launch:            Yes
  Supports MultiDevice Co-op Kernel Launch:      Yes
  Device PCI Domain ID / Bus ID / location ID:   0 / 1 / 0
  Compute Mode:
     < Default (multiple host threads can use ::cudaSetDevice() with device simultaneously) >

deviceQuery, CUDA Driver = CUDART, CUDA Driver Version = 11.6, CUDA Runtime Version = 11.6, NumDevs = 1
Result = PASS
```

可查出 CUDA Capability 版本号为 8.6。
