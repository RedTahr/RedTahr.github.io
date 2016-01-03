my notes from the pluralsight course on using a raspberry pi as a home server.

# remote into pi
ssh <ip> -l pi
pw: ...

passwd to change the default password

sudo apt-get install tightvncserver
## tightVNC
sudo vncserver :1
pw: (limitied to eight characters)

on windows/remote-host:
tightvnc client (or vnc viewer) ip:screen-number
<ip>:1

# home server

add apt sources
/etc/apt/sources.list.d
sudo nano webmin.list (create apt source file for webmin, rather than adding it to the main sources.list file in the parent folder)
sudo su (to elevate to root, for getting the gpg signing keys from www.webmin.com/deb.html)
exit (to stop being root)
sudo apt-get install webmin

## webmin 
https://<ip>:10000 from remote-host

## PLEX
http://<ip>:32400/web/index.html

## mounting filesystems
## partitioning and formating external drive
sudo parted
print all
select /dev/sda (or the label of the drive you're interested in)
mklabel gpt (to create new label for drive, all data will be lost)
### create partition for pi OS
mkpart
os (or preferred partition name)
ext4 (file system type)
0gb
8gb (or at least as big as the sd card you're currently running pi from) 
### create partition for data
mkpart data ext4 8gb 50% (mkpart == make partition)
mkpart data-ntfs ntfs 50% 100%
exit
ignore fstab info/warning for now.
### format partitions
sudo mkfs.ext4 /dev/sda2 (mkfs == make filesystem, has a variant for each file system type)
sudo mkfs.ntfs -Q -L data /dev/sda3 (-Q quick format, -L label)
### mount device
cd /mnt (from root)
sudo mkdir data
sudo mkdir data-ntfs
sudo touch data/placeholder
sudo touch data-ntfs/placeholder
sudo mount /dev/sda2 /mnt/data
sudo mount /dev/sda3 /mnt/data-ntfs
### fstab
cat /etc/fstab
sudo blkid (to get UUID for attached devices)
sudo nano /etc/fstab
'add lines for partitions'
/dev/disk/by-uuid/"DEVICE_UUID" /mnt/data   ext4    defaults,noatime    0   0
/dev/disk/by-uuid/"DEVICE_UUID" /mnt/data-ntfs  ntfs    defaults,noatime    0   0
(noatime - no access time logging, i.e. files won't be updated with their last accessed time, saves overhead)
exit and save file
sudo reboot

## public shares
sudo apt-get install samba samba-common-bin
cd /mnt/data
sudo mkdir /mnt/data/public
sudo mkdir /mnt/data-ntfs/public
ls -l (long/verbose listing)s
### setup samba in webmin
refresh modules to get webmin to know that samba is now installed
servers > samba
set "be a WINS server" (WINS - Windows Internet Name Service)
create file share
provide share name (i.e. data), and share path (i.e. /mnt/data/public)
create
click new file share and edit security
set "writable" and "guest access = Yes"
save
save (again)
repeat for data-ntfs share
scroll down, restart samba servers
### adding write permissions to ext4 file share
navigate to /mnt/data
sudo chmod go+w public (chmod == change mode, go+w == group & other add write mode, for public folder)

### choosing server name
default is raspberrypi on the network
sudo nano /etc/hosts
change last line to your preferred name

### setup samba from the command line
sudo nano /etc/samba/smb.conf
cp smb.conf smb.conf.master (to create a backup first if you like)
- you can see the changes webmin made, such as wins support being on (and probably
repeated later in a commented block too from the original file), see end of file for
created shares.
testparm (to verify smb.conf file)
sudo service samba restart (if you've made changes)

## DLNA media server
http://www.htpcguides.com/install-plex-media-server-on-raspberry-pi-2/
for current instructions on installing plexmediaserver (adding sources, etc)
sudo apt-get install plexmediaserver

http://<ip>:32400/web/index.html

### side note, was mentioned in the other dlna solution
to change the owner of a folder to a group so plex/dlna can write to an ext4 partition.
sudo chown 'plexdlna:plexdlna' /mnt/data/plexdlna (chown == change owner)
navigate to /mnt/data/public
mkdir music
sudo chown nobody:nogroup music (so you can drag/drop files into it from other places)

### manually edit plex folder locations
sudo service plexmediaserver stop
top (to check for any plex owned services still running)
sudo killall -u plex (to kill all user plex owned services

sudo mv /var/lib/plexmediaserver/ /mnt/data/plexmediaserver/
ls -l /mnt/data/plexmediaserver/ (to double check)
#### create symbolic link
sudo ln -s /mnt/data/plexmediaserver/ /var/lib/plexmediaserver/
ls /var/lib (to check the symbolic link was created)
sudo service plexmediaservice start








# CrashPlan
sudo apt-get install libjna-java
download from crashplan website
(for ease of use give everybody full access to /mnt/data/public)
sudo chmod -R ugo+rwx /mnt/data/public (-R == recursive, i.e. public and children)
tar zxvf CrashPlan_4.3.0_Linux.tgz (z==zip, x==extract, v==verbose, f==next argument is filename)
cd CrashPlan-install
sudo ./install.sh
go through the install process, remember to specify the directory to store local backups in,
i.e. /mnt/data/crashplan
service crashplan status (to check its working, but it won't be, as it assumes i586 of jre)
sudo rm -r /usr/local/crashplan/jre (delete wrongly installed copy)
sudo ln - s /user/lib/jvm/jdk-8-oracle-arm-vfp-hflt/ /usr/local/crashplan/jre
sudo service crashplan start (it'll probably start, but there are still errors)
cat /usr/local/crashplan/log/engine_error.log
cd /usr/local/crashplan
sudo rm libjtux.so
sudo wget http://www.jonrogers.co.uk/wp-content/uploads/2012/05/libjtux.so
sudo rm libmd5.so
sudo wget http://www.jonrogers.co.uk/wp-content/uploads/2012/05/libmd5.so
sudo nano /usr/local/crashplan/bin/CrashPlanEngine
ctrl-w full_cp= (search)
insert so it looks like:
FULL_CP="/usr/share/java/jna/jar:$TARGET_D.......
sudo service crashplan start
sudo service crashplan status
cat /usr/local/crashplan/log/engine_error.log (should return nothing)
cd /usr/local/crashplan
sudo update-rc.d crashplan defaults (to set it to restart on reboot)
## patch crashplans UI
sudo apt-get install libswt-gtk-3-java libswt-cairo-gtk-3-jni
sudo cp /usr/lib/java/swt-gtk-3.8.0.jar /usr/local/crashplan/lib/swt.jar



# OpenVPN
...





# BitTorrentSync (your own private cloud)
...



# Booting from External Drive
first force file system check on next reboot
sudo touch /forcefsck
reboot
watch the file system check if external monitor connected.

cat /boot/cmdline.txt
look for root=/dev/mmcblk0p2
sudo dd if=/dev/mmcblk0p2 of=/dev/sda1 bs=32M conv=noerror,sync
(if == input file, of == output file, bs == block size)
wait (dd command provides no progress status)

sudo e2fsck -f /dev/sda1
you'll probably have some block/inode errors, let the system fix them. mostly due to the copying a running file system.
if you have other errors revert to a non-erroneous pi state, or fix them or something... before continuing.

sudo resize2fs /dev/sda1 (resize, or use the gui pi settings option in x11)
sudo gdisk /dev/sda
i
1
record Partition unique GUID.
q

sudo nano /boot/cmdline.txt
change root=/dev/mmcblk0p2 to root=PARTUUID="unique-guid"
at the end of the line add rootdelay=5
exit/save/reboot
(power saving drives may dislike being booted from, so just revert the file if need be. it can be edited on any FAT compliant computer)
cat /etc/fstab
sudo blkid
copy sda1 UUID
sudo /etc/fstab
replace /dev/mmcblk0p2 with /dev/desk/by-uuid/"uuid"
exit/save/reboot
cat /etc/fstab
(if everythings changed then the pi's activity LED won't blink much, but the HDDs LED will)
## expand the swap file
swapon -s (status of swapfile)
sudo nano /etc/dphys/swap-file
change the one line to have, say 2048 for a 2gig swap file
reboot




# UPS (redundancy)
find a NUT (www.networkupstools.org) compatible UPS
...


# WordPress (server)
...






# supplmentary
## notes
- root folder of raspbian contains config.txt which defines the pi's boot settings and is user editable.
- 'nano' text editor
- edit /var/run/wpa_supplicant for wifi settings if not using xwin
- can backup the pi using win32diskimager to backup the sdcard as an image.
- sudo shutdown -h now
- groups (to see the groups your login belongs too, i.e. sudo, etc)

## permissions
http://permissions-calculator.org
four digits, i.e. 0755
special - user - group - other
special means different things depending on file. setuid = 4, setgid = 2, sticky = 1.
user/group/other : read = 4, write = 2, execute = 1.

## NTFS support
sudo apt-get install ntfs-config gdisk (ntfsprogs merged into ntfs-3g, which is preinstalled)

## pi current output
sudo nano /boot/config.txt
scroll to end
add 'max_usb_current=1'
increases usb current from 600mA to 1.2mA.

## top / htop
(processes, resources monitoring)
sudo apt-get install htop (not installed by default)
system processes