# MediaPipe Python Package for RaspberryPi OS on Raspberry Pi 3 / 4

## Project Homepage : https://github.com/google/mediapipe


## Installation
1. Install FFmpeg and OpenCV from official repository  *OR*  Build from sources using this [Guide](https://github.com/superuser789/MediaPipe-on-RaspberryPi/blob/main/BuildingFFMPEG%26OpenCV.md) .
```
sudo apt install ffmpeg  python3-opencv  python3-pip
```
Note: FFmpeg and OpenCV from official repository have been built with less optimisations. It is recommended to build from sources with the supported optimisations.

* Install these dependency packages if you have installed the [pre-built packages](https://github.com/superuser789/MediaPipe-on-RaspberryPi) without building from sources.
```
sudo apt install libxcb-shm0 libcdio-paranoia-dev libsdl2-2.0-0 libxv1  libtheora0 libva-drm2 libva-x11-2 libvdpau1 libharfbuzz0b libbluray2 libatlas-base-dev libhdf5-103 libgtk-3-0 libdc1394-22 libopenexr23
```



### On Raspberry Pi 4
#### 2. Install package
```
sudo pip3 install mediapipe-rpi4
```

#### To Uninstall package
```
sudo pip3 uninstall mediapipe-rpi4
```


### On Raspberry Pi 3
#### 2. Install package
```
sudo pip3 install mediapipe-rpi3
```

#### To Uninstall package
```
sudo pip3 uninstall mediapipe-rpi3
```


