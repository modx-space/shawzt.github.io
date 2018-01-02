---
layout: post
title:  Mount External Hard Drive On Raspbian
description: how to mount external hard drive on raspbian
modified: 2017-01-31
tags: raspberry
---

For Raspberry Pi, it only needs a normal SDcard burned with the runnable OS image, like [Raspbian][1]. And you may also need to expand the filesystem to ensure all of the SDcard storage is available to the OS, see [how to fix raspbian no space issue][2].

But that small SDcard is not enough and suitable to work as storage if we want to build something like [NAS][3], we need to attach additional hard drive to the Raspberry Pi to act as a powerful and trustable storage.

### Here is the guide that show you how to mount external hard drive on raspbian.

+ #### Check Attached Storage Devices
```
sudo fdisk -l
# we will mount /dev/sda2 to /mnt
```
![fdisk](/assets/images/fdisk.png)

+ #### Mount Device
```
sudo mount /dev/sda2 /mnt
# folder "/mnt" is the standard location for mounting, but it is up to you.
```
That's it, the drive is mounted.

+ #### Grant Permission
```
sudo chmod 775 /mnt
# verify
cd /mnt
ls
touch test.txt
```
Then done! So now we can read and write to the hardrive through Raspberry Pi!  
_If you still cannot do that even with `root` user, and occur the error 'Operation not permitted', please follow the solution here:_
  + _check whether the mounte drive is in __NTFS__ with command: `sudo blkid`_
  + _If so, you need to install lib __ntfs-3g__ with command: `sudo apt-get install ntfs-3g`_
  + _reboot RPi_

+ #### Automount
If you want to mount that drive from boot, we need to finish the last step!  
Add one more line `/dev/sda2 /mnt ntfs defaults,umask=0002 0 0` to file `/etc/fstab`(_split with `tab`_).
![fdisk](/assets/images/automount.png)
Try to reboot your RPi to verify the automounting. ✔️

---

### How to unmount?
```
sudo umount /mnt
```


[1]: https://www.raspberrypi.org/downloads/raspbian/
[2]: /posts/fix-raspbian-no-space
[3]: https://en.wikipedia.org/wiki/Network-attached_storage
