## Getting Started with Mesibo on Raspberry Pi
Instantly add real-time communication on your embedded devices by using Mesibo on Raspberry Pi device.
To build on Raspberry-Pi with Mesibo you can use the Mesibo C/C++ SDK - the low-level API which is extremely powerful, efficient, and light-weight.

Mesibo C/C++ library is available as a shared library(libmesibo.so) which can be seamlessly integrated into any application by compile time linking or by loading it dynamically. You can even use it from other languages of your choice like Python, PHP, Matlab, etc. as all of these languages allow interfacing with C/C++ libraries.

## Requirements

### 1. Mesibo C/C++ SDK 

To install the C/C++ SDK, Mesibo provides a convenience script for installing Mesibo on all the supported platforms.
For detailed installation instructions, refer [Mesibo on Linux](https://mesibo.com/documentation/install/linux/#install-using-the-convenience-script).

```
curl -fsSL https://raw.githubusercontent.com/mesibo/libmesibo/master/install.sh | sudo bash -

```
### 2. Sqlite 
Install sqlite and sqlite development packages for your platform. An sqlite database is used by Mesibo,to store data on your device.

#### Debian/Ubuntu
```
sudo apt-get install sqlite3 libsqlite3-dev
```
#### Centos/RHEL/Fedora
```
sudo yum install sqlite3 sqlite3-devel
```

That’s All!
You can now begin tinkering with Mesibo on RaspberryPi !








