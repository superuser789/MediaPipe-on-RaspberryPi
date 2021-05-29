# MediaPipe on RaspberryPi

Mediapipe needs OpenCV and ffmpeg libraries. You can install both of them from official raspberry pi repository. But these are built without optimisations and hardware accerelation. These can be built with the supported optimisations as explained [here](https://github.com/superuser789/MediaPipe-on-RaspberryPi/blob/main/BuildingFFMPEG%26OpenCV.md).



## Building MediaPipe for Raspberry Pi 3 / 4

* First, Install Bazel by following the steps [here](https://github.com/koenvervloesem/bazel-on-arm).

* Install the required dependencies :
```
sudo apt install python3-dev protobuf-compiler
sudo apt install libopenexr-dev libopenexr23 libdc1394-22 libdc1394-22-dev libeigen3-dev
```
* In case FFmpeg is not installed from official repository. Remove pre-installed packages/libraries which interfere with the built FFmpeg libraries
```
sudo apt remove libavcodec-dev libavutil-dev libavformat-dev libswscale-dev libavutil56
```

* Remove exception statements from  `ImathVec.h` & `ImathMatrix.h` present in `/usr/include/OpenEXR/` to avoid `error: ISO C++17 does not allow dynamic exception specifications`
```
sudo sed -i "s/throw (IEX_NAMESPACE::MathExc)/ /g" /usr/include/OpenEXR/ImathVec.h
sudo sed -i "s/throw (IEX_NAMESPACE::MathExc)/ /g" /usr/include/OpenEXR/ImathMatrix.h
```
* Download the source code
```
wget https://github.com/google/mediapipe/archive/refs/tags/v0.8.4.tar.gz -O mediapipe-0.8.4.tar.gz
tar xvf mediapipe-0.8.4.tar.gz
cd mediapipe-0.8.4
```

* Make the changes in `opencv_linux.BUILD` & `ffmpeg_linux.BUILD` by pointing to opencv & ffmpeg libraries
```
sed -i "s/x86_64-linux-gnu/arm-linux-gnueabihf/g" third_party/opencv_linux.BUILD
sed -i "s/x86_64-linux-gnu/arm-linux-gnueabihf/g" third_party/ffmpeg_linux.BUILD
```

* In `opencv_linux.BUILD` comment `"include/opencv2/**/*.h*",` & uncomment `"include/opencv4/opencv2/**/*.h*"` under `hdrs`.  Similarly comment `"include/",` & uncomment `"include/opencv4/",` under `includes`. 

* Add the following in `third_party/BUILD` after [line](https://github.com/google/mediapipe/blob/master/third_party/BUILD#L115)
### In case of Raspberry Pi 4
```
        "CMAKE_CXX_FLAGS": "-march=armv8-a+crc+simd -mcpu=cortex-a72 -mfpu=neon-fp-armv8 -mtune=cortex-a72 -mfloat-abi=hard -O3",
        "ENABLE_NEON": "ON",
        "WITH_TENGINE": "ON",
        "ENABLE_VFPV4": "ON",
        "WITH_CAROTENE": "OFF",
```

### In case of Raspberry Pi 3
```
        "CMAKE_CXX_FLAGS": "-march=armv8-a+crc -mfpu=neon-vfpv4 -mtune=cortex-a53 -ftree-vectorize -mfloat-abi=hard -O3",
        "ENABLE_NEON": "ON",
        "WITH_TENGINE": "ON",
        "ENABLE_VFPV4": "ON",
        "WITH_CAROTENE": "OFF",
```
* Add these flags to enable neon and optimisations in `setup.py` after [line](https://github.com/google/mediapipe/blob/master/setup.py#L242) as posted by [arron2003]( https://github.com/arron2003) [here](https://github.com/google/mediapipe/issues/1629#issuecomment-814599336).
### In case of Raspberry Pi 4
```
        '--copt=-march=armv7-a',
        '--copt=-mfpu=neon-vfpv3',
        '--copt=-mcpu=cortex-a72',
        '--copt=-mtune=cortex-a72',
        '--copt=-mfloat-abi=hard',
        '--copt=-O3',
```
### In case of Raspberry Pi 3
```
        '--copt=-march=armv7-a',
        '--copt=-mfpu=neon-vfpv3',
        '--copt=-mtune=cortex-a53',
        '--copt=-ftree-vectorize',
        '--copt=-mfloat-abi=hard',
        '--copt=-O3',
```

* Build the package
```
python3 setup.py gen_protos && python3 setup.py bdist_wheel
```
* Install required python libraries
```
sudo python3 -m pip install absl-py attrs>=19.1.0 numpy protobuf>=3.11.4 six wheel
```
* Install it
```
cd dist; sudo python3 -m pip install mediapipe-0.8-cp37-cp37m-linux_armhf.whl --no-deps
```

## Pre-built Packages
I recommend you to build the packages yourself.
In case, you want to skip it. You can download [deb packages](https://github.com/superuser789/MediaPipe-on-RaspberryPi).
First install the common packages 
```
sudo dpkg -i 
```


## References :

1. Building FFmpeg & mpv : https://www.raspberrypi.org/forums/viewtopic.php?t=199775
2. Building OpenCV : https://www.pyimagesearch.com/2019/09/16/install-opencv-4-on-raspberry-pi-4-and-raspbian-buster/  https://learnopencv.com/build-and-install-opencv-4-for-raspberry-pi/
4. Building Bazel : https://github.com/koenvervloesem/bazel-on-arm
5. Building MediaPipe : https://google.github.io/mediapipe/getting_started/install.html#installing-on-debian-and-ubuntu
6. Optimisation flags : https://gist.github.com/fm4dd/c663217935dc17f0fc73c9c81b0aa845


