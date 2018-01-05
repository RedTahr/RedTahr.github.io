# raspberry pi based security system
(end goal, face and number plate recog, sends image to user)

starting from:
https://github.com/HackerHouseYT/Smart-Security-Camera

using pyimagesearch 20170904 guide to get openCV setup on a pi 2b running stretch.
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