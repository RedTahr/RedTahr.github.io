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

