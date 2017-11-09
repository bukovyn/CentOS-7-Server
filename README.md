<p align="center">
  <img src="https://github.com/bukovyn/CentOS-7-Server/blob/master/img/CentOSLogo.png" alt="CentOS" height="250">
</p>

### :book: Table of Contents

- [Installation](#installation)
    - [Bootable Drive](#bootable-drive)
- [Setup](#setup)
    - [Root Login](#root-login)
    - [New User](#new-user)
    - [Root Privileges](#root-privileges)
    - [Public Key Authentication](#public-key)
        - [ssh-copy-id](#ssh-copy-id)
        - [manually](#manually)
- [Configuration](#configuration)
    - [Networking](#networking)
    - [Hostname](#hostname)
    - [Update & Upgrade](#update-upgrade)
    - [SSH](#ssh)
    - [SELinux](#selinux)
    - [FirewallD](#firewalld)
- [Additional Steps](#additional-steps)
    - [Adding Additional Drives](#adding-additional-drives)
      - [Assumptions](#assumptions)
      - [Finding Your Drive](#finding-your-drive)
      - [Creating Linux Partition(s)](#creating-linux-partitions)
      - [Creating a File System](#creating-file-system)
      - [Mounting a File System](#mounting-file-system)
      - [Automount File System On Boot](#automount-file-system)
      
 
<a name="installation"></a> 
## :computer: Installation

You can download CentOS [here](https://www.centos.org/download/). This guide follows installation and configuration for the [minimal version](http://isoredirect.centos.org/centos/7/isos/x86_64/CentOS-7-x86_64-Minimal-1708.iso) of CentOS.

<a name="bootable-drive"></a>
### Bootable Drive

:grey_exclamation: *Note: I will not be covering how to mount the image on a CD-ROM as it is mainly platform dependent and varies on which OS you are using, if this is the case, you can easily find out how to do this with a quick google search.*

:warning: *WARNING: Burning a bootable image to a USB flash drive will format the drive and wipe all data on it, please use an empty drive or one with disposable data.*

The most common and most convenient method for most would be to make a bootable flash drive, any USB with sufficient space should be fine. [Etcher](https://etcher.io/) is a fantastic tool for burning images to your flash drive and it is available on Windows, macOS, and Linux. Once you have [downloaded CentOS](https://www.centos.org/download/), the image burning process is extremely simple. With your flash drive inserted, follow these steps on Etcher *(or if you have a preffered image burning tool, feel free to use that!)*


<p align="center">
  <img src="https://github.com/bukovyn/CentOS-7-Server/blob/master/img/Etcher.gif" alt="Etcher" height="350">
</p>

With your bootable image mounted on your flash drive, we are ready to begin the setup process. Insert the drive on the machine where you want your server on and boot it up *(or restart it)*. In most cases, we will have to set the boot priority in BIOS so that the system boots from your flash drive rather than from any other existing drives on it. Right as the system is booting there will be a prompt to press some key *(usually `Delete` or `F12` to enter BIOS settings)*. Not every BIOS is the same but changing the booty priority is usually on the main page, make sure your flash drive is first in boot priority, save the adjusted settings and reboot. On reboot you should enter the CentOS setup process.
<a name="setup"></a> 
## :computer: Setup

<a name="root-login"></a>
### Root Login

<a name="new-user"></a>
### New User

<a name="root-privileges"></a>
### Root Priviliges

<a name="public-key"></a>
### Public Key

<a name="ssh-copy-id"></a>
#### ssh-copy-id

<a name="manually"></a>
#### Manually

<a name="configuration"></a> 
## :computer: Configuration

<a name="networking"></a> 
### Networking

Most likely, after the minimal install of CentOS, you will not be able to connect to the network because the Ethernet interfaces are not enabled. 

To see a list of ethernet cards installed on your machine, run the `nmcli d` command as shown with sample output:
```
$ nmcli d
DEVICE  TYPE      STATE      CONNECTION
enp0s3  ethernet  connected  enp0s3
lo      loopback  unmanaged  --
```
:grey_exclamation: *Note: In code snippets with more than one line, `$` denotes user input into the command line. It is not part of the command, it is simply there to help distinguish input from output. Lines without `$` are output from the console as a result of the command. Code snippets with only one line will always be input in this guide.*

Great, the system recognizes your ethernet device.

Now lets run the command `nmtui` :

<p align="center">
  <img src="https://github.com/bukovyn/CentOS-7-Server/blob/master/img/nmtui1.png" alt="nmtui1" height="300">
</p>
<p align="center"><i>Select the "Edit a connection" option.</i></p>

<p align="center">
  <img src="https://github.com/bukovyn/CentOS-7-Server/blob/master/img/nmtui2.png" alt="nmtui2" height="450">
</p>
<p align="center"><i>Select the "Edit" option.</i></p>

<p align="center">
  <img src="https://github.com/bukovyn/CentOS-7-Server/blob/master/img/nmtui3.png" alt="nmtui3" height="400">
</p>
<p align="center"><i>Finally, make sure your IPv4 configuration is set to automatic and "Automatically connect" is checked, finish with "OK"</i></p>
  
After we exit the `nmtui` interface, lets restart our network by running `service network restart`.

Now the server will get IP address from DHCP. Verify your IP by running `ip addr` on the command line. Furthermore, we can check if we have an active connection by pinging Google:

```
$ ping -c3 8.8.8.8
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=57 time=5.75 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=57 time=6.45 ms
64 bytes from 8.8.8.8: icmp_seq=3 ttl=57 time=6.85 ms

--- 8.8.8.8 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2010ms
rtt min/avg/max/mdev = 5.752/6.356/6.859/0.462 ms
```

If you receive packets back, congratulations, you are connected.

<a name="hostname"></a>
### Hostname

Check the currently assigned `HOSTNAME` with either `echo $HOSTNAME` or `hostname`.

If you wish to change this, simply edit the `/etc/hostname` file to your desired name by typing:

```
vi /etc/hostname
```
or
```
vim /etc/hostname
```
:star: *Tip: install `vim` by running `yum -y install vim`. `vim` is an enhanced version of the default `vi` that comes with the operating system.*

To confirm the change, logout and login again and check the new hostname with `echo $HOSTNAME`.

<a name="update-upgrade"></a>
### Update & Upgrade

This will only update and install the latest versions of already installed packages, it will not install any new ones so no need to worry about that.

You can either run:

```
yum update && yum upgrade
```
or
```
yum -y update && yum -y upgrade
```

Although keep in mind the first version of the command is recommended as the `-y` flag assumes yes when prompted if you want the updates to take place. It is good practice to review the changes that are about to take place if you are not sure what exactly is being updated.

<a name="ssh"></a> 
### SSH

[SSH](https://linux.die.net/man/1/ssh) stands for Secure Shell. It is the default protocol for remote management in Linux and it comes with our minimal install of CentOS.

To check the currently installed version:

```
ssh -V
```

It is generally a good idea to disable root login and allow users to connect as root only after they have logged in to their normal user account. This is for security purposes as it prevents the possibily of someone [brute forcing](https://en.wikipedia.org/wiki/Brute-force_attack) their way into your server.

To do this, we edit the `/etc/ssh/sshd_config` file and change `PermitRootLogin yes` to `PermitRootLogin no`:

```
vi /etc/ssh/sshd_config
```

After these changes, restart the service for it to take effect:

```
systemctl restart sshd.service
```

<a name="selinux"></a> 
### SELinux

You can edit the `/etc/sysconfig/selinux` file from the command line, this file is a symlink to `/etc/selinux/config`. The following is a sample output of the [`selinux`](https://www.systutorials.com/docs/linux/man/8-selinux/) file:

```
$ cat /etc/sysconfig/selinux
# This file controls the state of SELinux on the system.
# SELINUX= can take one of these three values:
#       enforcing - SELinux security policy is enforced.
#       permissive - SELinux prints warnings instead of enforcing.
#       disabled - SELinux is fully disabled.
SELINUX=enforcing
# SELINUXTYPE= type of policy in use. Possible values are:
#       targeted - Only targeted network daemons are protected.
#       strict - Full SELinux protection.
SELINUXTYPE=targeted

# SETLOCALDEFS= Check local definition changes
SETLOCALDEFS=0
```

Why would I want to change the SELinux configurations? Great question, you might not want to. 

So, do your research. If it is a local server only you are using, you can go ahead and disable it, whereas if this is a server that will be in use by multiple people then maybe disabling it isn't the best option. If you are still unsure as to what to do, I would recommend setting it to permissive. 

So, just `vi /etc/sysconfig/selinux` and set it to whichever option suits you.

Additionally, [`getenforce`](https://linux.die.net/man/8/getenforce) tells you the current mode of SELinux, [`setenforce 0`](https://linux.die.net/man/8/setenforce) temporarily puts SELinux into permissive mode and [`setenforce 1`](https://linux.die.net/man/8/setenforce) temporarily puts SELinux into enforcing mode. To disable it, you must go into the `selinux` file as explained above.

<a name="firewalld"></a> 
### FirewallD

[`firewalld`](https://www.unix.com/man-page/centos/1/firewalld/) is a firewall service that manages the server dynamically. It is installed by default on CentOS.  Flushing and recreating of new rules are not required and changes are applied on the go.

Generally, if it is a local network behind a [NAT](https://en.wikipedia.org/wiki/Network_address_translation) router, you don't need to worry about `firewalld` and can disable it by:
```
$ systemctl stop firewalld
$ systemctl disable firewalld
```
But I would take the time to read up on the `firewalld` man pages and decide for yourselves whether or not it is a good idea to disable the service.

If you disabled it and need to enable it again, the command is:

```
$ systemctl enable firewalld
$ systemctl start firewalld
```

:star: *Tip: You can always check which state the service is in by running the command `firewall-cmd --state`.*








<a name="additional-steps"></a> 
## :computer: Additional Steps

<a name="adding-additional-drives"></a> 
### Adding Additional Drives

<a name="assumptions"></a> 
#### Assumptions

The following steps assume that the new drive has been installed and is recognized by the system. A good way to verify this is to enter the BIOS during the system's boot process and ensure that the drive is recognized by the BIOS. If it is not recognized, the BIOS may have a menu option to scan for new drives. If this does not help or the scan for new drives option does not exist on your system, double check the physical aspects of the drive and make sure it is connected properly.

<a name="finding-your-drive"></a> 
#### Finding Your Drive

If the above assumptions are correct and your drive is recognized, it should be detected automatically by CentOS. Typically, the convention for drive names begin with hd or sd followed by a letter to indicate the device. For example, the first device could be `/dev/sda` or `/dev/hda`, the second `/dev/sdb` or `/dev/hdb`, and so on.

The following command shows the output with only ony physical drive on the system:
```
$ ls /dev/sd*
/dev/sda  /dev/sda1  /dev/sda2  /dev/sdc
```
:grey_exclamation: *Note: If you get the error - `ls: cannot access /dev/sd*: No such file or directory`. Try `ls /dev/hd*`*.

This shows the disk drive `/dev/sda` as well as two of it's partitions, `/dev/sda1` and `/dev/sda2`. In this case, `/dev/sdc` is the CD-ROM. Most of the time, when a new drive is added it will be assigned to `/dev/sdb`.

The following output is what is seen when a second drive is installed:
```
$ ls /dev/sd*
/dev/sda  /dev/sda1  /dev/sda2  /dev/sdb  /dev/sdc
```
The new drive is recognized as `/dev/sdb`.

:grey_exclamation: *Note: `/dev/sdb` currently has no partitions shown, which we must create along with file systems and then mount them for access. This is explained in the following steps.*

<a name="creating-linux-partitions"></a> 
#### Creating Linux Partition(s)

Now we must create one or more partitions on the disk. In order to partition the new drive `/dev/sdb`, we can use the [`fdisk`](http://www.tldp.org/HOWTO/Partition/fdisk_partitioning.html) command `fdisk /dev/sdb`.

Running `fdisk`:
```
$ Command (m for help):
```

`p` shows our drives partitions, as you can see, there are currently none.
```
$ Command (m for help): p

Disk /dev/sda: 640.1 GB, 640135028736 bytes, 1250263728 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0x8ac86c1a

   Device Boot      Start         End      Blocks   Id  System
```
:grey_exclamation: *Note: If this drive has previous partitions on it which you would like to get rid of, `d` deletes a partition, after deletion, `w` writes the modification to the disk.*

:warning: *WARNING: Deleting a partition and writing it to the disk is a "semi"-permanent action, technically you can recover the deleted partition data but it is more trouble than it's worth. Think twice before deleting a partition.*

To create a new partition, we run `n`:
```
$ Command (m for help): n
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
$ Select (default p): p
$ Partition number (1-4, default 1):
$ First sector (2048-1250263727, default 2048):
Using default value 2048
$ Last sector, +sectors or +size{K,M,G} (2048-1250263727, default 1250263727):
Using default value 1250263727
Partition 1 of type Linux and of size 596.2 GiB is set
```
Here we can either select `p` for a primary partition type or `e` for an extended partition type. Since it is an empty drive, we select a primary partition type.

Then we are prompted for a partition number, it is best to go by default and press `Enter` with no input. 

Lastly, we are prompted for partition size, this is entirely up to you and the purpose of your drive. For examples sake, if you wanted one partition that used all the space on your new drive, simply press `Enter` twice with no input as this will default to the first available block to the last available block.

The last step is to write the specified partition to the disk by typing `w`:
```
$ Command (m for help): w
The partition table has been altered!

Calling ioctl() to re-read partition table.
Syncing disks.
```
Running `ls /dev/sd*` *(the command used to see devices which we ran earlier)*, we can now see the new partition as `dev/sdb1`:
```
$ ls /dev/sd*
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
Once this directory exists, we can manually mount the file system using... *you guessed it!* The [`mount`](http://man7.org/linux/man-pages/man8/mount.8.html) command:
```
mount /dev/sdb1 /mydata
```

:star: *Tip: Running the `mount` command with no arguments shows all currently mounted file systems.*

<a name="automount-file-system"></a> 
#### Automount File System On Boot

For the new file system to be automatically mounted at boot time, an entry needs to be appended to the `/etc/fstab` file.

Simply run:

```
vi /etc/fstab 
```

The following is a sample output of the fstab file configured to automount our /mydata partition:
```
$ cat /etc/fstab
/dev/VolGroup00/LogVol00 /                      ext3    defaults        1 1
LABEL=/boot             /boot                   ext3    defaults        1 2
tmpfs                   /dev/shm                tmpfs   defaults        0 0
devpts                  /dev/pts                devpts  gid=5,mode=620  0 0
sysfs                   /sys                    sysfs   defaults        0 0
proc                    /proc                   proc    defaults        0 0
/dev/VolGroup00/LogVol01 swap                   swap    defaults        0 0
LABEL=/mydata           /mydata                 ext3    defaults        1 2
```

**Only the last line is important.** 
With this configuration line added to the fstab file, the file system will now automount on the next system boot. 

...and there it is, anything stored in the `/mydata` directory will be stored on the new disk's memory!




