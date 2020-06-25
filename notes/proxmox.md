# my notes to remind me about setting up proxmox

edit sources to remove enterprise and add no-subscription (search for current packages page)
comment out the enterprise repo in:
nano /etc/apt/sources.list.d/pve-enterprise.list 
add the no-sub repo in:
nano /etc/apt/sources.list.d/pve-no-sub.list
shutdown -r now

## turn off 'no subscription' dialog
grep -n 'Active' proxmox-widget-toolkit
change if (data.status !== 'Active') to if (false)
turn browser off and back on (maybe clear cache)

## reclaim drive space currently used by local-lvm
remove from datacenter > storage
1.2 Unmount and Delete lvm-thin
umount /dev/pve/data
lvremove /dev/pve/data
Confirm to delete
1.3 Check free space
vgdisplay pve | grep Free
1.4 Create new lvm
Note: Replace 92482 with the number you get from step 3, which is available free space
lvcreate -l 92482 -n data pve
1.5 Format and mount
mkfs.ext4 /dev/pve/data
mkdir /mnt/data
mount /dev/pve/data /mnt/data
Note: Use “mkfs.ext4” for ext4 format, “mkfs.xfs -f” to use xfs format
1.6 Modify fstab, so that it’s mounted on boot, add following line to the end of the file
nano /etc/fstab
/dev/pve/data /mnt/data ext4 defaults 0 0

## map other drives as needed
## put iso files in images folder of chosen drive

## add another drive to VM
ls /dev/disk/by-id/
root@pve:~# ls /dev/disk/by-id/
qm set VM-ID -virtio2 /dev/disk/by-id/DISK-ID

## references:
- https://serenity-networks.com/how-to-update-proxmox-ve-when-you-do-not-have-a-valid-subscription/
- https://johnscs.com/remove-proxmox51-subscription-notice/
- https://askubuntu.com/questions/113733/how-do-i-correctly-mount-a-ntfs-partition-in-etc-fstab
- https://dannyda.com/2020/05/10/how-to-delete-remove-local-lvm-from-proxmox-ve-pve-and-some-lvm-basics-commands/
- https://johnkeen.tech/proxmox-physical-disk-to-vm-only-2-commands/
-- https://docs.fedoraproject.org/en-US/quick-docs/creating-windows-virtual-machines-using-virtio-drivers/index.html
