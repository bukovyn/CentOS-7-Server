<p align="center">
  <img src="https://github.com/bukovyn/CentOS-7-Server/blob/master/img/CentOSLogo.png" alt="CentOS" height="250">
</p>

#### A guide (in progress) to configuring a minimal install of CentOS to set up a local server.

**Table of Contents**

- [Installation](#installation)
    - [CD](#cd)
    - [USB](#usb)
- [Setup](#setup)
    - [Root Login](#root-login)
    - [New User](#new-user)
    - [Root Privileges](#root-privileges)
    - [Public Key Authentication](#public-key)
        - [ssh-copy-id](#ssh-copy-id)
        - [manually](#manually)
- [Configuration](#configuration)
    - [SSH Daemon](#sshd)
    - [Networking](#networking)
    - [SELinux](#selinux)
    - [Firewall](#firewall)
- [Additional Steps](#additional-steps)
    - [Adding Additional Drives](#adding-additional-drives)
      - [Assumptions](#assumptions)
      - [Finding Your Drive](#finding-your-drive)
      - [Creating Linux Partition(s)](#creating-linux-partitions)
      - [Creating a File System](#creating-file-system)
      - [Mounting a File System](#mounting-file-system)
      - [Automount File System On Boot](#automount-file-system)
      
 
<a name="installation"></a> 
## Installation

<a name="cd"></a>
### CD

<a name="usb"></a>
### USB

<a name="setup"></a> 
## Setup

<a name="root-login"></a>
### Root Login

<a name="new-user"></a>
### New User

<a name="root-priviliges"></a>
### Root Priviliges

<a name="public-key"></a>
### Public Key

<a name="ssh-copy-id"></a>
#### ssh-copy-id

<a name="manually"></a>
#### Manually

<a name="configuration"></a> 
## Configuration

<a name="sshd"></a> 
### SSH Daemon

<a name="networking"></a> 
### Networking

<a name="selinux"></a> 
### SELinux

<a name="firewall"></a> 
### Firewall

<a name="additional-steps"></a> 
## Additional Steps

<a name="adding-additional-drives"></a> 
### Adding Additional Drives

<a name="assumptions"></a> 
#### Assumptions

The following steps assume that the new drive has been installed and is recognized by the system. A good way to verify this is to enter the BIOS during the system's boot process and ensure that the drive is recognized by the BIOS. If it is not recognized, the BIOS may have a menu option to scan for new drives. If this does not help or the scan for new drives option does not exist on your system, double check the phsyical aspects of the drive and make sure it is connected properly.

<a name="finding-your-drive"></a> 
#### Finding Your Drive

If the above assumptions are correct and your drive is recognized, it should be detected automatically by CentOS. Typically, the convention for drive names begin with hd or sd followed by a letter to indicate the device. For example, the first device could be /dev/sda or /dev/hda, the second /dev/sdb or /dev/hdb, and so on.

The following command shows the output with only ony physical drive on the system:
```
ls /dev/sd*
/dev/sda  /dev/sda1  /dev/sda2  /dev/sdc
```
This shows the disk drive /dev/sda as well as two of it's partitions, /dev/sda1 and /dev/sda2. In this case, /dev/sdc is the CD-ROM. Most of the time, when a new drive is added it will be assigned to /dev/sdb.

The following output is what is seen when a second drive is installed:
```
ls /dev/sd*
/dev/sda  /dev/sda1  /dev/sda2  /dev/sdb  /dev/sdc
```
The new drive is recognized as /dev/sdb.

*Note: /dev/sdb currently has no partitions shown, which we must create along with file systems and then mount them for access. This is explained in the following steps.*

<a name="creating-linux-partitions"></a> 
#### Creating Linux Partition(s)

Now we must create one or more partitions on the disk. In order to partition the new drive `/dev/sdb`, we can use the [`fdisk`](http://www.tldp.org/HOWTO/Partition/fdisk_partitioning.html) command `fdisk /dev/sdb`.

Running `fdisk`:
```
Command (m for help):
```

`p` shows our drives partitions, as you can see, there are currently none.
```
Command (m for help): p

Disk /dev/sda: 640.1 GB, 640135028736 bytes, 1250263728 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0x8ac86c1a

   Device Boot      Start         End      Blocks   Id  System
```

To create a new partition, we run `n`:
```
Command (m for help): n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): p
Partition number (1-4, default 1):
First sector (2048-1250263727, default 2048):
Using default value 2048
Last sector, +sectors or +size{K,M,G} (2048-1250263727, default 1250263727):
Using default value 1250263727
Partition 1 of type Linux and of size 596.2 GiB is set
```
Here we can either select `p` for a primary partition type or `e` for an extended partition type. Since it is an empty drive, we select a primary partition type.

Then we are prompted for a partition number, it is best to go by default and press `Enter` with no input. 

Lastly, we are prompted for partition size, this is entirely up to you and the purpose of your drive. For examples sake, if you wanted one partition that used all the space on your new drive, simply press `Enter` twice with no input as this will default to the first available block to the last available block.

The last step is to write the specified partition to the disk by typing `w`:
```
Command (m for help): w
The partition table has been altered!

Calling ioctl() to re-read partition table.
Syncing disks.
```
Running `ls /dev/sd*` (the command used to see devices which we ran earlier), we can now see the new partition as `dev/sdb1`:
```
ls /dev/sd*
/dev/sda  /dev/sda1  /dev/sda2  /dev/hsdb  /dev/sb1  /dev/sdc
```
The next step is to create a file system on our newly made partition.

<a name="creating-file-system"></a> 
#### Creating File System

The easiest way to create a file system on a partition is to use the [`mkfs.ext3`](https://linux.die.net/man/8/mkfs.ext3) utility. 
```
/sbin/mkfs.ext3 -L /mydata /dev/sdb1
```
This will create a file system on the specified disk partition, in our case `/dev/sdb1` with the label "mydata".

<a name="mounting-file-system"></a> 
#### Mounting File System

After we have created the new file system on our disk's partition, we need to mount it in order for it to be accessible to CentOS and its users. For this to be possible, we must create a mount point, which is a directory or folder into which the file system will be mounted. To keep it simple we will create a /mydata directory to match our file system label, keep in mind it is not necessary that these values match:
```
mkdir /mydata
```
Once this directory exists, we can manually mount the filesystem using... you guessed it! The [`mount`](http://man7.org/linux/man-pages/man8/mount.8.html) command:
```
mount /dev/sdb1 /mydata
```

*Tip: Running the `mount` command with no arguments shows all currently mounted file systems.*

<a name="automount-file-system"></a> 
#### Automount File System On Boot

For the new file system to be automatically mounted at boot time, an entry needs to be appended to the `/etc/fstab` file.

Simply run:

```
vi /etc/fstab 
```
or
```
vim /etc/fstab
```
*Note: install `vim` by running `yum -y install vim`. `vim` is an enhanced version of the default `vi` that comes with the operating system.*

The following is a sample output of the fstab file configured to automount our /mydata partition:
```
/dev/VolGroup00/LogVol00 /                      ext3    defaults        1 1
LABEL=/boot             /boot                   ext3    defaults        1 2
tmpfs                   /dev/shm                tmpfs   defaults        0 0
devpts                  /dev/pts                devpts  gid=5,mode=620  0 0
sysfs                   /sys                    sysfs   defaults        0 0
proc                    /proc                   proc    defaults        0 0
/dev/VolGroup00/LogVol01 swap                   swap    defaults        0 0
LABEL=/mydata           /mydata                 ext3    defaults        1 2
```

Only the **last line** is important, with this configuration line added to the fstab file, the file system will now automount on the next system boot. 




