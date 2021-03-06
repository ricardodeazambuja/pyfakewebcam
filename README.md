# pyfakewebcam

An API for writing RGB frames to a fake webcam device on Linux!

**Compatible with Python2.7 and Python3.x**

**Author:** John Emmons
**Email:** mail@johnemmons.com

**Disclaimer:** I wrote this project when I was a university
student. I now employed full time so I don't have time to keep things
update or add new features :(. Please feel free to fork!

## installation

```
# use pip to get the latest stable release (NOT THIS FORK)
pip install pyfakewebcam

# use git to install the latest version
git clone https://github.com/jremmons/pyfakewebcam.git
cd pyfakewebcam
python setup.py install
```

## dependencies
```
# python
pip install numpy

# linux
sudo apt-get install v4l2loopback-utils

# linux (optional)
sudo apt-get install python-opencv # 10x performance improvement if installed (see below)
sudo apt-get install ffmpeg # useful for debugging
```

## performance

When I run the `examples/example.py` script (640x360 resolution)
on an Intel i7-3520M (2.9GHz, turbos to 3.6 GHz), the time to
schedule a single frame is *~3 milliseconds* (with opencv
installed). **You can use this library without installing opencv**,
but it is almost 10x slower; time to schedule a frame without
opencv is *~26 milliseconds* (RGB to YUV conversion done with
numpy operations).

If your goal is to run at 30Hz (or slower) and ~26 milliseconds of
delay is acceptable in your application, then opencv may not be
necessary.

## usage

Insert the v4l2loopback kernel module.

```
modprobe v4l2loopback devices=2 # will create two fake webcam devices
```

Example code.

```python
# see red_blue.py in the examples dir
import time
import pyfakewebcam
import numpy as np

blue = np.zeros((480,640,3), dtype=np.uint8)
blue[:,:,2] = 255

red = np.zeros((480,640,3), dtype=np.uint8)
red[:,:,0] = 255

camera = pyfakewebcam.FakeWebcam('/dev/video2', 640, 480)

while True:

    camera.schedule_frame(red)
    time.sleep(0.5)

    camera.schedule_frame(blue)
    time.sleep(0.5)
```

Run the following command to see the output of the fake webcam.
```
ffplay /dev/video2
```

## Caveats:
0. Clone and use `pip3 install .` or  `pip3 install git+git://github.com/ricardodeazambuja/pyfakewebcam --upgrade`
1. After that, try this: ```sudo modprobe v4l2loopback video_nr=2 card_label="fake webcam" exclusive_caps=1```
2. Finally, run one of the examples (if it doesn't work with /dev/video2, try another) and [test it directly on your browser](https://webrtc.github.io/samples/src/content/devices/input-output/).
3. If you need to change anything, first run ```sudo modprobe -r v4l2loopback```
4. You may need to [install v4l2loopback from source](https://github.com/umlaeute/v4l2loopback) if the commands above fail.