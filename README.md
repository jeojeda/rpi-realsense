# rpi-realsense
setup of a raspberry pi 4 and Realsense Camera D405

## Instructions to Setup the Rasperry Pi case
1.setup case
https://www.youtube.com/watch?v=hWH3v-YUM1E

2.heat sink
https://www.youtube.com/watch?v=E-4GaAz7XNM

## Instructions to Install rpi image

## Instructions to connect with SSH
On Windows CMD
    ```
    ssh <username>@<hostname> #example rpi@raspberrypi
    type password
    ```

Update
    ```
    ~ $ sudo apt update
    ```

turn off
    ```
    ~ $ sudo halt
    ```


## Enabling VNC Server at the command line
You can enable VNC Server at the command line using raspi-config:

```
~ $ sudo raspi-config
```
Now, enable VNC Server by doing the following:
Navigate to Interfacing Options.
Scroll down and select VNC › Yes.

Install and run VNC viewer on windows (https://www.realvnc.com/en/connect/download/viewer/)
connect using the name of the local server:
raspberrypi

## Instructions to setup Raspberry Pi 4 and Intel Realsense
based on https://github.com/datasith/Ai_Demos_RPi/wiki/Raspberry-Pi-4-and-Intel-RealSense-D435

Start with updating, upgrading, and installing dependencies and tools:
```
sudo apt-get update && sudo apt-get dist-upgrade
sudo apt-get install automake libtool vim cmake libusb-1.0-0-dev libx11-dev xorg-dev libglu1-mesa-dev
```
Expand the filesystem by selecting the Advanced Options menu entry, and select yes to rebooting:
```
sudo raspi-config
```
Increase swap to 2GB by changing the file below to CONF_SWAPSIZE=2048:
```
sudo vi /etc/dphys-swapfile
```
Apply the change:
```
sudo /etc/init.d/dphys-swapfile restart swapon -s
```

From datasith youtube
```
sudo apt-get install automake libtool vim cmake libusb-1.0.0-dev libx11-dev xorg-dev libglu1-mesa-dev
```

```
mkdir Work
cd Work/
git clone https://github.com/IntelRealSense/librealsense.git
```
Create a new udev rule:
```
cd librealsense
sudo cp config/99-realsense-libusb.rules /etc/udev/rules.d/ 
```
Apply the change (needs to be run by root):
```
sudo su
udevadm control --reload-rules && udevadm trigger
exit
```
Modify the path by adding the following line to the .bashrc file:
```
vi ~/.bashrc
```
At the end of the file add:
```
# add LD_LIBRARY_PATH environment variable
export LD_LIBRARY_PATH=/usr/local/lib:$LD_LIBRARY_PATH
```
to exit type ":x"

Apply the change:
```
source ~/.bashrc
```
```
cd ..
git clone --depth=1 -b v3.10.0 https://github.com/google/protobuf.git
cd protobuf
./autogen.sh
./configure
make -j1
sudo make install

cd python
export LD_LIBRARY_PATH=../src/.libs
python3 setup.py --cpp_implementation 
python3 setup.py test --cpp_implementation
sudo python3 setup.py install --cpp_implementation
export PROTOCOL_BUFFERS_PYTHON_IMPLEMENTATION=cpp
export PROTOCOL_BUFFERS_PYTHON_IMPLEMENTATION_VERSION=3
sudo ldconfig
protoc --version

cd ../..
```
Install libtbb-dev parallelism library for C++:
```
cd Work/
wget https://github.com/PINTO0309/TBBonARMv7/raw/master/libtbb-dev_2018U2_armhf.deb
sudo dpkg -i libtbb-dev_2018U2_armhf.deb
sudo ldconfig
rm libtbb-dev_2018U2_armhf.deb
```

Install RealSense SDK librealsense (inside Work folder):
```
cd ~/librealsense
mkdir  build  && cd build
cmake .. -DBUILD_EXAMPLES=true -DCMAKE_BUILD_TYPE=Release -DFORCE_LIBUVC=true
## not used by datasith ## make -j1
sudo make install
```

error ssl. Try to fix
```
sudo apt-get install libssl-dev
```

after error, repeat:

####Install RealSense SDK librealsense (inside Work folder):
```
cd ~/librealsense
mkdir  build  && cd build
cmake .. -DBUILD_EXAMPLES=true -DCMAKE_BUILD_TYPE=Release -DFORCE_LIBUVC=true
make -j1
sudo make install
```

Install RealSense SDK pyrealsense2 Python bindings for librealsense:
```
cd ~/librealsense/build
cmake .. -DBUILD_PYTHON_BINDINGS=bool:true -DPYTHON_EXECUTABLE=$(which python3)
make -j1
sudo make install
```
Modify the path by adding the following line to the .bashrc file:
```
vi ~/.bashrc
export PYTHONPATH=$PYTHONPATH:/usr/local/lib
```
Apply the change:
type ":x"
```
source ~/.bashrc
```
Install OpenGL:
```
sudo apt-get install python-opengl
sudo -H pip3 install pyopengl
sudo -H pip3 install pyopengl_accelerate==3.1.3rc1
```
Change pi settings (enable OpenGL):
```
sudo raspi-config
"7. Advanced Options" – "A8 GL Driver" – "G2 GL (Fake KMS)"
```

to run Realsense viewer
```
realsense-viewer
```




after error:
```
sudo apt update && sudo apt upgrade -y
sudo apt-get install gcc-7 g++-7
sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-7 60 --slave /usr/bin/g++ g++ /usr/bin/g++-7
sudo update-alternatives --set gcc "/usr/bin/gcc-7"
```
go to rpi@raspberrypi:~/Work/librealsense/build $
to fix the error runing again: cmake .. -DBUILD_PYTHON_BINDINGS=bool:true -DPYTHON_EXECUTABLE=$(which python3)
```
sudo apt-get install --reinstall gcc
```
