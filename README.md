# Rover Configuration

## Setup Wifi
```bash
sudo sh -c 'wpa_passphrase SSID PASSPHRASE >> /etc/wpa_supplicant/wpa_supplicant.conf'
```

## update & upgrade
```bash
sudo apt update
sudo apt upgrade -y
```

## Install Samba
```bash
sudo apt install samba -y
sudo echo "[pi]
path = /home/pi
read only = No
guest ok = Yes
force user = pi
" >> /etc/samba/smb.conf
```

## Install cmake
```bash
sudo apt-get install -y cmake libboost-all-dev
```

## Install python3 & pip
```bash
sudo apt-get install -y python3-dev 
sudo apt-get install -y python-dev python-numpy

wget https://bootstrap.pypa.io/get-pip.py
sudo python3 get-pip.py
```

## Make use of virtual environments for Python development
```bash
sudo pip install virtualenv virtualenvwrapper==4.8.4
sudo rm -rf ~/get-pip.py ~/.cache/pip
```

To finish the install of these tools, we need to update our ~/.profile  file (similar to .bashrc  or .bash_profile ).
Using a terminal text editor such as vi, add the following lines to your ~/.profile 
```bash
sudo echo "
# virtualenv and virtualenvwrapper
export WORKON_HOME=$HOME/.virtualenvs
export VIRTUALENVWRAPPER_PYTHON=/usr/bin/python3
source /usr/local/bin/virtualenvwrapper.sh
" >> ~/.profile
```

## Create a virtual environment to hold OpenCV 4 and additional packages
```bash
source .profile
mkvirtualenv py3cv4 -p python3
workon py3cv4
```

## Install NumPy
```bash
pip install numpy
```

## Install OpenCV
### install dependences
```bash
sudo apt-get install -y build-essential
sudo apt-get install -y cmake git libgtk2.0-dev pkg-config libavcodec-dev libavformat-dev libswscale-dev
sudo apt-get install -y libgtk-3-dev
sudo apt-get install -y libtbb2 libtbb-dev libjpeg-dev libpng-dev libtiff-dev libjasper-dev libdc1394-22-dev
sudo apt-get install -y libv4l-dev libxvidcore-dev libx264-dev
sudo apt-get install -y libcanberra-gtk* #a package which may reduce pesky GTK warnings
sudo apt-get install -y libatlas-base-dev gfortran # two packages which contain numerical optimizations for OpenCV
```

### clone opencv repository
```bash
git clone https://github.com/opencv/opencv.git -b 4.0.1
git clone https://github.com/opencv/opencv_contrib.git -b 4.0.1
```

### cmake
```bash
cd ~/
cd opencv
mkdir build
cd build
cmake -D CMAKE_BUILD_TYPE=RELEASE \
    -D CMAKE_INSTALL_PREFIX=/usr/local \
    -D OPENCV_EXTRA_MODULES_PATH=~/opencv_contrib/modules \
    -D ENABLE_NEON=ON \
    -D ENABLE_VFPV3=ON \
    -D BUILD_TESTS=OFF \
    -D OPENCV_ENABLE_NONFREE=ON \
    -D INSTALL_PYTHON_EXAMPLES=OFF \
    -D BUILD_EXAMPLES=OFF ..
```

### extend swapfile in order not to run out the memory while building
```bash
sudo vi /etc/dphys-swapfile
```
In /etc/dphys-swapfile,
```
+ # CONF_SWAPSIZE=100
+ CONF_SWAPSIZE=2048 
```
```bash
sudo /etc/init.d/dphys-swapfile stop 
sudo /etc/init.d/dphys-swapfile start
swapon
```

### Build
```
make -j4
sudo make install
sudo ldconfig
```

### Roll back the swapsize and restart the service, dphys-swapfile
```bash
sudo vi /etc/dphys-swapfile
```
In /etc/dphys-swapfile,
```
+ CONF_SWAPSIZE=100
+ # CONF_SWAPSIZE=2048 
```
```bash
sudo /etc/init.d/dphys-swapfile stop 
sudo /etc/init.d/dphys-swapfile start
swapon
```

## Misc. 
### GPS
```bash
sudo apt-get install libgps-dev gaps-clients
sudo echo "
USBAUTO=“false”
DEVICES=“/dev/ttyAMA0 /dev/pps0”
GPSD_OPTTIONS=“-n”
" >> /etc/default/gpsd

sudo systemctl start gpsd
sudo systemctl enable gpsd
```

```
sudo apt-get install pls-tools
sudo vi /boot/config.txt
```
In /boot/config.txt, 
```
dtoverlay=pps-gpio,gpiopin=[GPIO Number]
```

Test
```bash
sudo pastiest /dev/pps0
```


