1.准备工作

升级gcc到4.8版本：

```
#curl -Lks http://www.hop5.in/yum/el6/hop5.repo > /etc/yum.repos.d/hop5.repo
#yum install gcc gcc-g++ -y
#gcc --version
#g++ --version
```

升级binutils至2.25：

```
#wget http://ftp.gnu.org/gnu/binutils/binutils-2.25.1.tar.bz2
#tar -xjf binutils-2.25.1.tar.bz2
#cd binutils-2.25.1
#./configure
#make
#make install
#as --version
#objdump -v
#ld -v
```

CMake：

```
#wget -c https://github.com/Kitware/CMake/releases/download/v3.14.1/cmake-3.14.1.tar.gz  #不用最新的也可以
```

下载OpenCV：

```
#wget -c https://github.com/opencv/opencv/archive/3.4.5.zip
#yum -y install epel-release
```

github比较慢，可以用这个：[https://zh.osdn.net/projects/sfnet_opencvlibrary/releases/](https://zh.osdn.net/projects/sfnet_opencvlibrary/releases/)。

```
#wget -c https://sourceforge.mirrorservice.org/o/op/opencvlibrary/3.4.5/OpenCV%203.4.5.zip
```

安装Nux Dextop Yum源：

```
#rpm --import http://li.nux.ro/download/nux/RPM-GPG-KEY-nux.ro
#rpm -Uvh http://li.nux.ro/download/nux/dextop/el6/x86_64/nux-dextop-release-0-2.el6.nux.noarch.rpm
```

安装ffmpeg（在Nux Dextop上）：

```
#yum install ffmpeg ffmpeg-devel -y
```

其它的依赖项：

```
#yum install -y gcc gcc-c++ make cmake
#yum install -y ant ImageMagick ImageMagick-devel GraphicsMagick
#yum install -y autoconf automake
#yum install -y libpng-devel libjpeg-devel libtiff-devel
#yum install -y jasper-devel freetype-devel
#yum install -y libtool-ltdl libtool-ltdl-devel* libtool-ltdl libtool-ltdl-devel libgnomeui-devel
#yum install -y camke-gui pkgconfig gtk2 gtk2-devel gtk2-devel-docs gimp-devel gimp-devel-tools gimp-help-browser gstreamer-devel gstreamer-plugins-base-devel libv4l libv4l-devel mencoder flvtool2 libavc1394-devel libraw1394-devel libdc1394-devel jasper-utils zlib-devel unzip swig libtool nasm gnome-devel gnome-devel-docs gcc-gfortran
```

检查一下：

```
#ant --help
#convert -version  #ImageMagick
#gm  #GraphicsMagick
```

或者：

```
#gm convert -list formats
```

2.安装OpenCV

 先解压

```
#unzip 3.4.5.zip
#cd opencv-3.4.5
```

修改源码： `modules/videoio/src/cap_v4l.cpp` 的245行：

```
#define V4L2_CID_ISO_SENSITIVITY (V4L2_CID_CAMERA_CLASS_BASE+23)
#endif

// https://github.com/opencv/opencv/issues/13929
#ifndef V4L2_CID_MPEG_VIDEO_H264_VUI_EXT_SAR_HEIGHT
#define V4L2_CID_MPEG_VIDEO_H264_VUI_EXT_SAR_HEIGHT (V4L2_CID_MPEG_BASE+364)
#endif
#ifndef V4L2_CID_MPEG_VIDEO_H264_VUI_EXT_SAR_WIDTH
#define V4L2_CID_MPEG_VIDEO_H264_VUI_EXT_SAR_WIDTH (V4L2_CID_MPEG_BASE+365)
#endif

/* Defaults - If your board can do better, set it here.  Set for the most common type inputs. */
#define DEFAULT_V4L_WIDTH  640
#define DEFAULT_V4L_HEIGHT 480
```

编译：

```
#mkdir build
#cd build
#cmake -DCMAKE_BUILD_TYPE=Release -DBUILD_SHARED_LIBS=OFF -DBUILD_EXAMPLES=OFF -DBUILD_TESTS=OFF -DBUILD_PERF_TESTS=OFF -DBUILD_TIFF=ON -DCMAKE_INSTALL_PREFIX=/usr/local ..
```

看情况要不要加入`-DWITH_GPHOTO2=OFF`。

安装：

```
#make
#make install
```
