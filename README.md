# MediaPipe on RaspberryPi
Building MediaPipe on Raspberry Pi 3

Mediapipe needs OpenCV and ffmpeg libraries. You can install both of them from official raspberry pi repository. But these are built without optimisations and hardware accerelation. Lets build these first with the supported optimisations.

# Building FFMPEG (for mpv)
There is quite detailed guide available for building it for raspberry pi here. https://www.raspberrypi.org/forums/viewtopic.php?t=199775
In short, first build the dependencies - fdk-aac, mp3lame, libass, x264.

For FFMPEG:
Download the latest source from here: https://ffmpeg.org/releases/

wget https://ffmpeg.org/releases/ffmpeg-4.4.tar.bz2  -O ffmpeg.tar.bz2
tar xvf ffmpeg.tar.bz2
cd ffmpeg-4*

./configure \
--prefix=/usr \
--enable-gpl \
--enable-nonfree \
--enable-static \
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
--enable-libmp3lame

make -j4
sudo checkinstall -y --pkgname ffmpeg --pkgversion 4.4.0 make install
sudo dpkg -i ffmpeg_4.4.0-1_armhf.deb


# Building OpenCV for Raspberr Pi
Follow the detailed guides from here & here. https://www.pyimagesearch.com/2019/09/16/install-opencv-4-on-raspberry-pi-4-and-raspbian-buster/ and 
https://learnopencv.com/build-and-install-opencv-4-for-raspberry-pi/

In short, First install the dependency packages and libraries.
Download sources :
wget https://github.com/opencv/opencv/archive/refs/tags/4.5.2.tar.gz -O opencv-4.5.2.tar.gz
wget https://github.com/opencv/opencv_contrib/archive/refs/tags/4.5.2.tar.gz -O opencv_contrib-4.5.2.tar.gz
tar xvf opencv*
- Note Full path of modules directory in opencv_contrib and replace '/home/pi/cv/opencv_contrib-4.5.2/modules' with your modules path

cd opencv-4.5.2
mkdir build ; cd build
cmake -D CMAKE_BUILD_TYPE=RELEASE \
    -D CMAKE_INSTALL_PREFIX=/usr \
    -D OPENCV_EXTRA_MODULES_PATH=/home/pi/cv/opencv_contrib-4.5.2/modules \
    -D CMAKE_CXX_FLAGS='-march=armv8-a+crc -mfpu=neon-vfpv4 -mtune=cortex-a53 -ftree-vectorize -mfloat-abi=hard -O3'
    -D ENABLE_NEON=ON \
    -D ENABLE_VFPV4=ON \
    -D BUILD_TESTS=OFF \
    -D INSTALL_PYTHON_EXAMPLES=OFF \
    -D OPENCV_ENABLE_NONFREE=ON \
    -D CMAKE_SHARED_LINKER_FLAGS=-latomic \
    -D WITH_CAROTENE=OFF \
    -D BUILD_EXAMPLES=OFF ..

make -j4
sudo checkinstall -y --pkgname opencv --pkgversion 4.5.2 make install
sudo dpkg -i opencv_4.5.2-1_armhf.deb


# Building Mediapie for Raspberry Pi 3
First, build bazel by following the steps here. https://github.com/koenvervloesem/bazel-on-arm

Install the required dependencies :
sudo apt install python3-dev protobuf-compiler
sudo apt install libopenexr-dev libopenexr23 libdc1394-22 libdc1394-22-dev libeigen3-dev

Remove exception statements from  ImathVec.h & ImathMatrix.h to avoid : error: ISO C++17 does not allow dynamic exception specifications
sudo sed -i "s/throw (IEX_NAMESPACE::MathExc)/ /g" /usr/include/OpenEXR/ImathVec.h
sudo sed -i "s/throw (IEX_NAMESPACE::MathExc)/ /g" /usr/include/OpenEXR/ImathMatrix.h

wget https://github.com/google/mediapipe/archive/refs/tags/v0.8.4.tar.gz -O mediapipe-0.8.4.tar.gz
tar xvf mediapipe-0.8.4.tar.gz
cd mediapipe-0.8.4
Make the changes in opencv_linux.BUILD & ffmpeg_linux.BUILD pointing to opencv & ffmpeg libraries
sed -i "s/x86_64-linux-gnu/arm-linux-gnueabihf/g" third_party/opencv_linux.BUILD
sed -i "s/x86_64-linux-gnu/arm-linux-gnueabihf/g" third_party/ffmpeg_linux.BUILD

In opencv_linux.BUILD. comment "include/opencv2/**/*.h*", & uncomment "include/opencv4/opencv2/**/*.h*". Similarly under hdrs and comment "include/", & uncomment "include/opencv4/", under includes 

Add the following in third_party/BUILD after https://github.com/google/mediapipe/blob/master/third_party/BUILD#L115 
        "CMAKE_CXX_FLAGS": "-march=armv8-a+crc -mfpu=neon-vfpv4 -mtune=cortex-a53 -ftree-vectorize -mfloat-abi=hard -O3",
        "ENABLE_NEON": "ON",
        "WITH_TENGINE": "ON",
        "ENABLE_VFPV4": "ON",
        "WITH_CAROTENE": "OFF",




References :

Building ffmpeg & mpv : https://www.raspberrypi.org/forums/viewtopic.php?t=199775
