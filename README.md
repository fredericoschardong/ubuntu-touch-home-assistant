# Home Assistant on Nexus 4 and others

I wasn't able to get the latest [cryptography](https://pypi.org/project/cryptography/) package installed on Ubuntu 16.04 (Ubuntu Touch latest version is still 16.04). Therefore, I opted for Python 3.7 and the [latest version of home assistant that supports it](https://pypi.org/project/homeassistant/2021.1.5/).

## Install Ubuntu Touch
Install [Ubuntu Touch](https://github.com/ubports/ubports-installer/). For my Nexus 4 I had to select the devel channel.

Once UT is running on the device, go in Settings => About and activate Developer Mode. You will need to set a passcode for Developer Mode.

From the desktop connect through adb or ssh to the device:  
`user@ubuntu-desktop$ adb shell`  

## Create a container
`phablet@ubuntu-phablet:~$ libertine-container-manager create -i hass`  


## Install dependencies
```phablet@ubuntu-phablet:~$ libertine-container-manager exec -i hass -c bash
root@ubuntu-phablet:/# apt-get install software-properties-common
root@ubuntu-phablet:/# add-apt-repository ppa:deadsnakes/ppa
root@ubuntu-phablet:/# apt update
root@ubuntu-phablet:/# apt install python3.7* autoconf automake libtool
root@ubuntu-phablet:/# export CRYPTOGRAPHY_ALLOW_OPENSSL_102=1
```

## Install Home Assistant
```root@ubuntu-phablet:/# python3.7 -m venv homeassistant
root@ubuntu-phablet:/# cd homeassistant
root@ubuntu-phablet:/# source bin/activate
root@ubuntu-phablet:/# python3.7 -m pip install -U pip
root@ubuntu-phablet:/# python3.7 -m pip install pillow==7.2.0 --global-option="build_ext" --global-option="--disable-zlib" --global-option="--disable-jpeg"
root@ubuntu-phablet:/# python3.7 -m pip install homeassistant
```

## Start Home Assistant

Still in the virtual environment run:  
`root@ubuntu-phablet:/# hass`
First run will take 10-15 minutes.
Point the Nexus 4 browser to:  
`http://localhost:8123`

Configuration files are stored in /home/phablet/.cache/libertine-container/hass/rootfs/home/phablet/.homeassistant/.
To use scp (or WinSCP) to access this folder from the desktop, configure ssh:  
`user@ubuntu-desktop$ ssh-keygen`  
`user@ubuntu-desktop$ adb push ~/.ssh/id_rsa.pub /home/phablet/`  
`user@ubuntu-desktop$ adb shell`  
`phablet@ubuntu-phablet:~$ mkdir /home/phablet/.ssh`  
`phablet@ubuntu-phablet:~$ chmod 700 /home/phablet/.ssh`  
`phablet@ubuntu-phablet:~$ cat /home/phablet/id_rsa.pub >> /home/phablet/.ssh/authorized_keys`  
`phablet@ubuntu-phablet:~$ chmod 600 /home/phablet/.ssh/authorized_keys`  
`phablet@ubuntu-phablet:~$ chown -R phablet.phablet /home/phablet/.ssh`  
`phablet@ubuntu-phablet:~$ sudo android-gadget-service enable ssh`
`user@ubuntu-desktop$ ssh phablet@ubuntu-phablet`  


