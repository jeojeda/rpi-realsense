# rpi-realsense
setup of a raspberry pi 4 and Realsense Camera D405

## Instructions to Setup the Rasperry Pi case
1.setup case
https://www.youtube.com/watch?v=hWH3v-YUM1E

2.heat sink
https://www.youtube.com/watch?v=E-4GaAz7XNM

## Install rpi image with Raspberry Pi Imager
1. general instructions and steps from:
https://www.youtube.com/watch?v=LBIBUntnxp8

## Instructions to connect with SSH and some basics commands
On Windows CMD
    ```
    ssh <username>@<hostname> #example rpi@raspberrypi
    type password
    ```
    
to list the command lines that have been written ```sudo cat ~/.bash_history ```

to turn off
    ```
    ~ $ sudo halt
    ```
## Start the setup of the Raspberry Pi:
- Update
    ```
    ~ $ sudo apt update
    ```

- Enabling VNC Server at the command line
You can enable VNC Server at the command line using raspi-config:
```
~ $ sudo raspi-config
```
Enable VNC Server by doing the following:
- Navigate to Interfacing Options.
- Scroll down and select VNC 
- Select Yes.

Install and run VNC viewer on windows (https://www.realvnc.com/en/connect/download/viewer/)
- connect using the name of the local server:
- for example: raspberrypi

### Instructions to setup Raspberry Pi 4 and Intel Realsense
based on https://github.com/datasith/Ai_Demos_RPi/wiki/Raspberry-Pi-4-and-Intel-RealSense-D435

- Start with updating, upgrading, and installing dependencies and tools:
```
sudo apt-get update && sudo apt-get dist-upgrade
```
- Expand the filesystem by selecting the Advanced Options menu entry, and select yes to rebooting:
```
sudo raspi-config
```
- Increase swap to 2GB by changing the file below to CONF_SWAPSIZE=2048:
```
sudo nano /etc/dphys-swapfile
```
- Apply the change:
```
sudo /etc/init.d/dphys-swapfile restart swapon -s
```

*From datasith youtube*
```
sudo apt-get install automake libtool vim cmake libusb-1.0.0-dev libx11-dev xorg-dev libglu1-mesa-dev
```

```
git clone https://github.com/IntelRealSense/librealsense.git
```
- Create a new udev rule:
```
cd librealsense
sudo cp config/99-realsense-libusb.rules /etc/udev/rules.d/ 
```
- Apply the change (needs to be run by root):
```
sudo su
udevadm control --reload-rules && udevadm trigger
exit
```
- Modify the path by adding the following line to the .bashrc file:
```
sudo nano ~/.bashrc
```
- At the end of the file add:
```
# add LD_LIBRARY_PATH environment variable
export LD_LIBRARY_PATH=/usr/local/lib:$LD_LIBRARY_PATH
# to save type: Ctrl + X
# type Yes
```

- Apply the change:
```
source ~/.bashrc
```
- Download and configure Protobuf
```
cd ..
git clone --depth=1 -b v3.10.0 https://github.com/google/protobuf.git
cd protobuf
./autogen.sh
./configure
make -j1
sudo make install
```
- Install the Wrappers
```
cd python
export LD_LIBRARY_PATH=../src/.libs
sudo python3 setup.py build --cpp_implementation 
sudo python3 setup.py install --cpp_implementation
sudo python3 setup.py test --cpp_implementation
export PROTOCOL_BUFFERS_PYTHON_IMPLEMENTATION=cpp
export PROTOCOL_BUFFERS_PYTHON_IMPLEMENTATION_VERSION=3
sudo ldconfig
protoc --version
cd ../..
```
- Install libtbb-dev parallelism library for C++:
```
wget https://github.com/PINTO0309/TBBonARMv7/raw/master/libtbb-dev_2018U2_armhf.deb
sudo dpkg -i libtbb-dev_2018U2_armhf.deb
sudo ldconfig
rm libtbb-dev_2018U2_armhf.deb
```

- NEW CODE TO INSTALL LIBREALSENSE
reference, comment from devenjarvis
https://github.com/IntelRealSense/librealsense/issues/9962
```
sudo apt install clang llvm
cd ~/librealsense
mkdir build && cd build
export CC=/usr/bin/clang
export CXX=/usr/bin/clang++
cmake .. -DBUILD_EXAMPLES=true -DCMAKE_BUILD_TYPE=Release -DFORCE_LIBUVC=true -DOTHER_LIBS="-latomic" 
make -j4
sudo make install
```
# Continue with the regular code from Realsense

- Install RealSense SDK pyrealsense2 Python bindings for librealsense:
```
cd ~/librealsense/build
cmake .. -DBUILD_PYTHON_BINDINGS=bool:true -DPYTHON_EXECUTABLE=$(which python3)
make -j1
sudo make install
```
- Modify the path by adding the following line to the .bashrc file:
```
sudo nano ~/.bashrc
export PYTHONPATH=$PYTHONPATH:/usr/local/lib
```
Apply the change:
type ":x"
```
source ~/.bashrc
```
- Install OpenGL:
```
sudo apt-get install python-opengl
sudo -H pip3 install pyopengl
sudo -H pip3 install pyopengl_accelerate==3.1.3rc1
```
- Change pi settings (enable OpenGL):
```
sudo raspi-config
"7. Advanced Options" – "A8 GL Driver" – "G2 GL (Fake KMS)"
```

- to run Realsense viewer
```
realsense-viewer
```






# reset ssh on windows when the rpi is new.
goto 
```
C:\Users\USER NAME\.ssh```
then change the extension of the known_hosts to *.txt
Run SSH from CMD. Accept the new key. Type Yes

# references ##https://service.tu-dortmund.de/en/linux-mit-wpa-supplicant-wlan
## https://linuxhint.com/rasperberry_pi_wifi_wpa_supplicant/
# To move a file. Example crt file to eduroam
from Download folder:
```sudo mv rootcert.crt /etc/ssl/certs```
add the network text to the supplicant file
```cd /etc/wpa_supplicant/
sudo nano wpa_supplicant.conf```

