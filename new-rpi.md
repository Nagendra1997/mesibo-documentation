
## Instructions for installing Mesibo on Raspberry Pi

Instantly add real-time communication on your embedded devices by using Mesibo on Raspberry Pi device.

Mesibo on Raspberry Pi is available as a shared library(.so) which allows you to use it from any application OR languages of your choice like C, C++, Python, PHP, Matlab etc.

### OS requirements
- Raspbian 

### Install Mesibo using the convenience script

Mesibo provides a convenience script for installing Mesibo on all the supported platforms.

> **Warning** : Always examine scripts downloaded from the internet before running them locally.


```
$ curl -fsSL https://raw.githubusercontent.com/mesibo/libmesibo/master/install.sh | sudo bash -

<output truncated>

```
Please note the following points:

- The scripts require root or sudo privileges to run. Therefore, you should carefully examine and audit the scripts before running them.

- The scripts attempt to detect your Linux distribution and version and configure your package management system for you.

- Convenience script overwrites existing mesibo version with the latest version without any confirmation.



### Install Sqlite 

Install sqlite and sqlite development package  . An sqlite database is used by Mesibo, to store data on your device.

```
sudo apt-get install sqlite3 libsqlite3-dev
```

That’s All!
You can now begin tinkering with Mesibo on RaspberryPi !


### Next steps

- Continue to Download Sample Code for Linux

- Continue with the User Guide.

