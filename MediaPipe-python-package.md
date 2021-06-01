# MediaPipe Python Package for RaspberryPi OS on Raspberry Pi 3 / 4

## Project Homepage : https://github.com/google/mediapipe


## Installation
1. Install FFmpeg and OpenCV from Official repository

Note: FFmpeg and OpenCV from official repository have been built without optimisations and hardware acceleration. These can be built from sources with the supported optimisations
```
sudo apt install ffmpeg python-opencv
```

*OR*
* Build from sources using this [Guide](https://github.com/superuser789/MediaPipe-on-RaspberryPi#readme) 


2. Install dependency packages 
```
sudo apt install libxcb-shm0 libcdio-paranoia-dev libsdl2-2.0-0 libxv1  libtheora0 libva-drm2 libva-x11-2 libvdpau1 libharfbuzz0b libbluray2 libatlas-base-dev libhdf5-103 libgtk-3-0 libdc1394-22 libopenexr23
```



### For Raspberry Pi 4
#### 3. Install package
```
sudo pip install mediapipe-rpi4
```

#### To Uninstall package
```
sudo pip uninstall mediapipe-rpi4
```


### For Raspberry Pi 3
#### 3. Install package
```
sudo pip install mediapipe-rpi3
```

#### To Uninstall package
```
sudo pip uninstall mediapipe-rpi3
```


