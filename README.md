----------------------

The following steps are based on the guide that [pfcouto](https://github.com/pfcouto) compiled and shared in 
https://github.com/datasith/Ai_Demos_RPi/issues/10 and [mathklk](https://github.com/mathklk/realsense_raspberry_pi4)

Visit the [wiki](https://github.com/jeojeda/rpi-realsense/wiki) for more general instructions about Git, How to setup a network, etc.

----------------------
# General Instructions
Setup of a raspberry pi 4 and Realsense Camera D405

## 1. Setup the Rasperry Pi case
1. setup case
https://www.youtube.com/watch?v=hWH3v-YUM1E

2. heat sink
https://www.youtube.com/watch?v=E-4GaAz7XNM

## 2. Install rpi image with Raspberry Pi Imager
1. general instructions and steps from:
https://www.youtube.com/watch?v=LBIBUntnxp8
Follow only the part of the RPI Image

## 3. Connect with SSH
On Windows CMD
    ```
    ssh <username>@<hostname> #example rpi@raspberrypi
    type password
    ```
## 4. Start the setup of the Raspberry Pi:
- Update
    ```
    ~ $ sudo apt update
    ~ $ sudo apt upgrade    
    ```

- Enabling VNC Server at the command line
You can enable VNC Server at the command line using raspi-config:
```
~ $ sudo raspi-config
```

## 5. Remote Operation
Enable VNC Server:
```
"3. Interface Options"
"I3. VNC Enable graphical remote access using RealVNC" - "Yes"
"2. Display Options"
"D5. VNC Rsolution" - "1280x720"

- Select "Finish" on the raspi-config menu, and select "Yes" to rebooting

- Install a VNC client on another computer, for example [VNC Viewer by RealVNC](https://www.realvnc.com/en/connect/download/viewer/)
- connect using the name of the local server, for example: ```raspberrypi```
```

----------------------
# Raspberry OS

```sudo apt-get update && sudo apt-get dist-upgrade```

- Expand the filesystem by selecting the Advanced Options menu entry, and select yes to rebooting:
```sudo raspi-config```

- Increase swap to 2GB by changing the file below to ```CONF_SWAPSIZE=2048:```
```sudo nano /etc/dphys-swapfile```
- Apply the change: ```sudo /etc/init.d/dphys-swapfile restart swapon -s```

# Raspberry Pi Setup
### Pre-install Requirements on Raspbian
- Start with updating, upgrading, and installing dependencies and tools:
```
sudo apt-get update && sudo apt-get dist-upgrade
sudo apt-get install -y automake libtool cmake libusb-1.0-0-dev libx11-dev xorg-dev libglu1-mesa-dev libssl-dev clang llvm libatlas-base-dev python3-opencv
```

From another source, ```vim``` was also installed. I prefer to use ```ǹano``` instead.

- Create a new ```udev``` rule:
```
cd ~
git clone https://github.com/IntelRealSense/librealsense.git
cd librealsense
sudo cp config/99-realsense-libusb.rules /etc/udev/rules.d/
```
- Apply the change (needs to be run by root):
```
sudo su
udevadm control --reload-rules && udevadm trigger
exit
```

- Modify the path by adding the following line to the ```.bashrc``` file:
```
sudo nano ~/.bashrc
export LD_LIBRARY_PATH=/usr/local/lib:$LD_LIBRARY_PATH
```
- Apply the change:
```
source ~/.bashrc
```
### After an error libtbb-dev was installed before the Python installation inside Protobuf
```
sudo apt-get install libtbb-dev
```

### Installation
- Install ```protobuf```* — Google's language-neutral, platform-neutral, extensible mechanism for serializing structured data:
```
cd ~
git clone --depth=1 -b v3.10.0 https://github.com/google/protobuf.git
cd protobuf
./autogen.sh
./configure
python --version # 3.9.2
make -j1
sudo make install
cd python
export LD_LIBRARY_PATH=../src/.libs
python3 setup.py build --cpp_implementation 
python3 setup.py test --cpp_implementation
sudo python3 setup.py install --cpp_implementation
export PROTOCOL_BUFFERS_PYTHON_IMPLEMENTATION=cpp
export PROTOCOL_BUFFERS_PYTHON_IMPLEMENTATION_VERSION=3
sudo ldconfig
protoc --version
```
- Install ```libtbb-dev``` parallelism library for C++:
```
cd ~
wget https://github.com/PINTO0309/TBBonARMv7/raw/master/libtbb-dev_2018U2_armhf.deb
sudo dpkg -i ~/libtbb-dev_2018U2_armhf.deb
sudo ldconfig
rm libtbb-dev_2018U2_armhf.deb
```
- Install RealSense SDK ```librealsense```:
```
sudo apt install clang llvm
sudo apt-get install libssl-dev
cd ~/librealsense
mkdir build
cd build
export CC=/usr/bin/clang
export CXX=/usr/bin/clang++
cmake .. -DBUILD_EXAMPLES=true -DCMAKE_BUILD_TYPE=Release -DFORCE_LIBUVC=true -DOTHER_LIBS="-latomic"

make -j2
sudo make install
```
- Install RealSense SDK ```pyrealsense2``` Python bindings for ```librealsense```:
```
cd ~/librealsense/build
cmake .. -DBUILD_PYTHON_BINDINGS=bool:true -DPYTHON_EXECUTABLE=$(which python3)
make -j2
sudo make install
```
- Modify the path by adding the following line to the ```.bashrc``` file:
```
echo "export PYTHONPATH=$PYTHONPATH:/usr/local/lib" >> .bashrc
```
- Apply the change:
```
source ~/.bashrc
```
- Install ```OpenGL```:
```
sudo apt-get install python-opengl
sudo -H pip3 install pyopengl
# if there is an error try
sudo apt get install python-opengl
sudo -H pip3 install pyopengl_accelerate
# if there is an error try
sudo pip3 install pyopengl
```
- Change pi settings (enable ```OpenGL```):
```
sudo raspi-config
"7. Advanced Options" – "A8 GL Driver" – "G2 GL (Fake KMS)"
```
- Run Realsense viewer
```
realsense-viewer
```






## * Debuging
- ERROR: [Could not initialize offscreen context! ](https://github.com/acrobotic/Ai_Demos_RPi/issues/1#issue-628919760)
```
pi@raspberrypi:~ $ realsense-viewer
Could not initialize offscreen context!
```
There is a difference between running vncserver from the terminal and using raspi-config to install it. So, if you see the below output you're doing it wrong
```
pi@raspberrypi:~ $ glxgears -info
GL_RENDERER = llvmpipe (LLVM 9.0.1, 128 bits)
GL_VERSION = 3.1 Mesa 19.3.2
GL_VENDOR = VMware, Inc.
```
Output known to be working:
```
GL_RENDERER = V3D 4.2
GL_VERSION = 2.1 Mesa 19.3.2
GL_VENDOR = Broadcom
```
The process to make the necessary changes is described in [raspberrypi/Raspberry-Pi-OS-64bit#21](https://github.com/raspberrypi/Raspberry-Pi-OS-64bit/issues/21)  and [raspberrypi/Raspberry-Pi-OS-64bit#3](https://github.com/raspberrypi/Raspberry-Pi-OS-64bit/issues/3) . It comes down to how we run ```vncserver```, running it from a Putty terminal uses a VMware driver, whereas running it via ```sudo raspi-config``` > ```interfacing options``` > ```vncserver``` > ```enable``` and directly login from the VNC client app then uses the necessary Broadcom driver.
- ERROR: [GLFW Driver Error: GLX: GLX version 1.3 is required](https://github.com/acrobotic/Ai_Demos_RPi/issues/1#issue-628919760) 
```
pi@raspberrypi:~ $ realsense-viewer
GLFW Driver Error: GLX: GLX version 1.3 is required
Could not initialize offscreen context!
```
## * Install Python 3.9.2
https://raspberrytips.com/install-latest-python-raspberry-pi/
- Download and extract
```
wget https://www.python.org/ftp/python/3.9.2/Python-3.9.2.tgz
tar -zxvf Python-3.9.2.tgz
cd Python-3.9.2
./configure --enable-optimizations
sudo make altinstall
```
- Enable the new version of Python as the version by default
```
cd /usr/bin
sudo rm python
sudo ln -s /usr/local/bin/python3.9 python
python --version
```
## * Error
error running ```./autogen.sh```
[autogen.sh: autoref: not found](https://github.com/meganz/MEGAcmd/issues/218)

first ```sudo apt install autoconf```
second ```sudo apt update```
third ```sudo apt install libtool```
