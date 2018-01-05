# raspberry pi based security system
(end goal, face and number plate recog, sends image to user)

starting from:
https://github.com/HackerHouseYT/Smart-Security-Camera

using pyimagesearch 20170904 guide to get openCV setup on a pi 2b running stretch, using python 3.
https://www.pyimagesearch.com/2017/09/04/raspbian-stretch-install-opencv-3-python-on-your-raspberry-pi/

# opencv dependencies
check all filesystem space has been used (is by default, but double check)
> df -h
if /dev/root isn't the size of your card, then run > sudo raspi-config and expand the file system.

### to save a gig of space
$ sudo apt-get purge wolfram-engine
$ sudo apt-get purge libreoffice*
$ sudo apt-get clean
$ sudo apt-get autoremove

$ sudo apt-get update && sudo apt-get upgrade

$ sudo apt-get install build-essential cmake pkg-config

$ sudo apt-get install libjpeg-dev libtiff5-dev libjasper-dev libpng12-dev

$ sudo apt-get install libavcodec-dev libavformat-dev libswscale-dev libv4l-dev
$ sudo apt-get install libxvidcore-dev libx264-dev

$ sudo apt-get install libgtk2.0-dev libgtk-3-dev

$ sudo apt-get install libatlas-base-dev gfortran

$ sudo apt-get install python2.7-dev python3-dev

# download opencv
git clone https://github.com/opencv/opencv.git
git clone https://github.com/opencv/opencv_contrib.git

# setup Python
$ wget https://bootstrap.pypa.io/get-pip.py
$ sudo python get-pip.py
$ sudo python3 get-pip.py
## with virtual environments
$ sudo pip install virtualenv virtualenvwrapper
$ sudo rm -rf ~/.cache/pip
## update ~/.profile
$ echo -e "\n# virtualenv and virtualenvwrapper" >> ~/.profile
$ echo "export WORKON_HOME=$HOME/.virtualenvs" >> ~/.profile
$ echo "source /usr/local/bin/virtualenvwrapper.sh" >> ~/.profile
## load the update profile
$ source ~/.profile

$ mkvirtualenv cv -p python3
## to get into the cv virtual environment
$ source ~/.profile
$ workon cv

(next command provides no progress indication and can take a minute...)
$ pip install numpy

# compile and install opencv
$ workon cv

$ cd ~/opencv-3.3.0/
$ mkdir build
$ cd build
$ cmake -D CMAKE_BUILD_TYPE=RELEASE \
    -D CMAKE_INSTALL_PREFIX=/usr/local \
    -D INSTALL_PYTHON_EXAMPLES=ON \
    -D OPENCV_EXTRA_MODULES_PATH=~/opencv_contrib-3.3.0/modules \
    -D BUILD_EXAMPLES=ON ..

to verify CMake output, check: https://www.pyimagesearch.com/wp-content/uploads/2017/08/python3_opencv-768x511.png

## configure swap space before compiling
/etc/dphys-swapfile  and then edit the CONF_SWAPSIZE=100 variable:
CONF_SWAPSIZE=1024
this gets all four cores on the pi running (and may thrash your sd card, so change it back when you're done)