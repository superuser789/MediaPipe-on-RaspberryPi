# FFmpeg (for mpv) & OpenCV for Raspberry Pi 3 / 4



## Building FFmpeg
There is quite detailed guide available for building it for raspberry pi [here](https://www.raspberrypi.org/forums/viewtopic.php?t=199775).

1. In short, first build the dependencies - fdk-aac, mp3lame, libass, x264.

2. Download the latest ffmpeg source from here: https://ffmpeg.org/releases/

```
wget https://ffmpeg.org/releases/ffmpeg-4.4.tar.bz2  -O ffmpeg.tar.bz2
tar xvf ffmpeg.tar.bz2
cd ffmpeg-*
```
3. Configure it
#### In case of Raspberry Pi 4
```
./configure \
--prefix=/usr \
--enable-gpl \
--enable-nonfree \
--enable-shared \
--enable-libtheora \
--enable-libvorbis \
--enable-omx \
--enable-omx-rpi \
--enable-mmal \
--enable-libxcb \
--enable-libfreetype \
--enable-libass \
--enable-gnutls \
--disable-opencl \
--enable-libcdio \
--enable-libbluray \
--extra-cflags="-march=armv8-a+crc+simd -mcpu=cortex-a72 -mfpu=neon-fp-armv8 -mtune=cortex-a72 -mfloat-abi=hard -O3" \
--enable-libx264 \
--enable-libfdk-aac \
--enable-libmp3lame \
--enable-avresample
```
#### In case of Raspberry Pi 3
```
./configure \
--prefix=/usr \
--enable-gpl \
--enable-nonfree \
--enable-shared \
--enable-libtheora \
--enable-libvorbis \
--enable-omx \
--enable-omx-rpi \
--enable-mmal \
--enable-libxcb \
--enable-libfreetype \
--enable-libass \
--enable-gnutls \
--disable-opencl \
--enable-libcdio \
--enable-libbluray \
--extra-cflags="-march=armv8-a+crc -mfpu=neon-vfpv4 -mtune=cortex-a53 -ftree-vectorize -mfloat-abi=hard -O3" \
--enable-libx264 \
--enable-libfdk-aac \
--enable-libmp3lame \
--enable-avresample
```
4. Build & Install it
```
make -j4
sudo checkinstall -y --pkgname ffmpeg --pkgversion 4.4.0 make install
sudo dpkg -i ffmpeg_4.4.0-1_armhf.deb
```

#
## Building OpenCV

Follow the detailed guides from [here](https://www.pyimagesearch.com/2019/09/16/install-opencv-4-on-raspberry-pi-4-and-raspbian-buster/) & [here](https://learnopencv.com/build-and-install-opencv-4-for-raspberry-pi/).

1. In short, First install the dependency packages and libraries.

2. Download sources :
```
wget https://github.com/opencv/opencv/archive/refs/tags/4.5.2.tar.gz -O opencv-4.5.2.tar.gz
wget https://github.com/opencv/opencv_contrib/archive/refs/tags/4.5.2.tar.gz -O opencv_contrib-4.5.2.tar.gz
tar xvf opencv*
```
```
cd opencv-4.5.2
mkdir build ; cd build
```
* Note full path of `modules` directory in `opencv_contrib` and replace `/home/pi/cv/opencv_contrib-4.5.2/modules` with your modules path.

3. Configure it
#### In case of Raspberry Pi 4
```
cmake -D CMAKE_BUILD_TYPE=RELEASE \
    -D CMAKE_INSTALL_PREFIX=/usr \
    -D OPENCV_EXTRA_MODULES_PATH=/home/pi/cv/opencv_contrib-4.5.2/modules \
    -D CMAKE_CXX_FLAGS='-march=armv8-a+crc+simd -mcpu=cortex-a72 -mfpu=neon-fp-armv8 -mtune=cortex-a72 -mfloat-abi=hard -O3'
    -D ENABLE_NEON=ON \
    -D ENABLE_VFPV4=ON \
    -D WITH_TENGINE=ON \
    -D BUILD_TESTS=OFF \
    -D INSTALL_PYTHON_EXAMPLES=OFF \
    -D OPENCV_ENABLE_NONFREE=ON \
    -D CMAKE_SHARED_LINKER_FLAGS=-latomic \
    -D WITH_CAROTENE=OFF \
    -D BUILD_EXAMPLES=OFF ..
```
#### In case of Raspberry Pi 3
```
cmake -D CMAKE_BUILD_TYPE=RELEASE \
    -D CMAKE_INSTALL_PREFIX=/usr \
    -D OPENCV_EXTRA_MODULES_PATH=/home/pi/cv/opencv_contrib-4.5.2/modules \
    -D CMAKE_CXX_FLAGS='-march=armv8-a+crc -mfpu=neon-vfpv4 -mtune=cortex-a53 -ftree-vectorize -mfloat-abi=hard -O3'
    -D ENABLE_NEON=ON \
    -D ENABLE_VFPV4=ON \
    -D WITH_TENGINE=ON \
    -D BUILD_TESTS=OFF \
    -D INSTALL_PYTHON_EXAMPLES=OFF \
    -D OPENCV_ENABLE_NONFREE=ON \
    -D CMAKE_SHARED_LINKER_FLAGS=-latomic \
    -D WITH_CAROTENE=OFF \
    -D BUILD_EXAMPLES=OFF ..
```
4. Build & Install it
```
make -j4
sudo checkinstall -y --pkgname opencv --pkgversion 4.5.2 make install
sudo dpkg -i opencv_4.5.2-1_armhf.deb
```
#
## Pre-built Packages
I recommend you to build the packages yourself.
In case, you want to skip it. You can download [pre-built packages](https://github.com/superuser789/MediaPipe-on-RaspberryPi).
1. Install dependency packages 
```
sudo apt install libxcb-shm0 libcdio-paranoia-dev libsdl2-2.0-0 libxv1  libtheora0 libva-drm2 libva-x11-2 libvdpau1 libharfbuzz0b libbluray2 libatlas-base-dev libhdf5-103 libgtk-3-0
```
2. Install the packages present in [common](https://github.com/superuser789/MediaPipe-on-RaspberryPi/tree/main/common) directory
```
sudo dpkg -i fdk-aac_2.0.2-1_armhf.deb  libass_0.15.1-1_armhf.deb  mp3lame_3.100-1_armhf.deb  x264_0.163-1_armhf.deb
```
3. Based on RaspberryPi [3](https://github.com/superuser789/MediaPipe-on-RaspberryPi/tree/main/RPi%203) or [4](https://github.com/superuser789/MediaPipe-on-RaspberryPi/tree/main/RPi%204), Install FFmpeg & OpenCV
```
sudo dpkg -i ffmpeg_4.4.0-1_armhf.deb  opencv_4.5.2-1_armhf.deb
```

### To Remove these FFmpeg & OpenCV packages
```
sudo apt remove fdk-aac mp3lame libass x264 ffmpeg
sudo apt remove opencv
```
#
