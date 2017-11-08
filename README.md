<p align="center">
  <img src="https://github.com/bukovyn/CentOS-7-Server/blob/master/img/CentOSLogo.png" alt="CentOS" height="250">
</p>

#### A guide (in progress) to configuring a minimal install of CentOS to set up a local server.

**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

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
- [Software Essentials](#software-essentials)
    - [vim](#vim)
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

<a name="software-essentials"></a> 
## Software Essentials

<a name="vim"></a> 
### Vim

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
```sh
ls /dev/sd*
/dev/sda  /dev/sda1  /dev/sda2  /dev/sdc
```
This shows the disk drive /dev/sda as well as two of it's partitions, /dev/sda1 and /dev/sda2. In this case, /dev/sdc is the CDROM. Most of the time, when a new drive is added it will be assigned to /dev/sdb.

The following output is what is seen when a second drive is installed:
```sh
ls /dev/sd*
/dev/sda  /dev/sda1  /dev/sda2  /dev/sdb  /dev/sdc
```
The new drive is recognized as /dev/sdb.
Note, /dev/sdb currently has no partitions shown, which we must create along with file systems and then mount them for access. This is explained in the following steps.

<a name="creating-linux-partitions"></a> 
#### Creating Linux Partition(s)


<a name="creating-file-system"></a> 
#### Creating File System

<a name="mounting-file-system"></a> 
#### Mounting File System

<a name="automount-file-system"></a> 
#### Automount File System On Boot




