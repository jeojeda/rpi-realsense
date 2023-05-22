## Errors running python example files

- Error ```AttributeError: module 'pyrealsense2' has no attribute 'pipeline'```
Try ```import pyrealsense2.pyrealsense2 as rs``` [reference](https://github.com/IntelRealSense/librealsense/issues/11671
) 



## + Raspberry Pi details
```cat /etc/debian_version```

- ```11.7```

```at /etc/os-release```
- ```PRETTY_NAME="Raspbian GNU/Linux 11 (bullseye)"
  NAME="Raspbian GNU/Linux"
  VERSION_ID="11"
  VERSION="11 (bullseye)"
  VERSION_CODENAME=bullseye
  ID=raspbian
  ID_LIKE=debian
  HOME_URL="http://www.raspbian.org/"
  SUPPORT_URL="http://www.raspbian.org/RaspbianForums"
  BUG_REPORT_URL="http://www.raspbian.org/RaspbianBugs"```
  
```uname -a```
 - ```Linux raspberrypi 6.1.21-v8+ #1642 SMP PREEMPT Mon Apr  3 17:24:16 BST 2023 aarch64 GNU/Linux```
 
 ```cat /proc/cpuinfo```
 - ```processor	: 0
    BogoMIPS	: 108.00
    Features	: fp asimd evtstrm crc32 cpuid
    CPU implementer	: 0x41
    CPU architecture: 8
    CPU variant	: 0x0
    CPU part	: 0xd08
    CPU revision	: 3

    processor	: 1
    BogoMIPS	: 108.00
    Features	: fp asimd evtstrm crc32 cpuid
    CPU implementer	: 0x41
    CPU architecture: 8
    CPU variant	: 0x0
    CPU part	: 0xd08
    CPU revision	: 3

    processor	: 2
    BogoMIPS	: 108.00
    Features	: fp asimd evtstrm crc32 cpuid
    CPU implementer	: 0x41
    CPU architecture: 8
    CPU variant	: 0x0
    CPU part	: 0xd08
    CPU revision	: 3

    processor	: 3
    BogoMIPS	: 108.00
    Features	: fp asimd evtstrm crc32 cpuid
    CPU implementer	: 0x41
    CPU architecture: 8
    CPU variant	: 0x0
    CPU part	: 0xd08
    CPU revision	: 3

    Hardware	: BCM2835
    Revision	: c03114
    Serial		: 100000009f7698d9
    Model		: Raspberry Pi 4 Model B Rev 1.4```
