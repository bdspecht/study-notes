RHCSA

# Understand and use essential tools

## Access a shell prompt and issues commands

**bash (bourne-again shell)** - based on the command-line interpreter by Stephen Bourne

**dash** - simpler but faster

**tcsh - **enhanced version of the Unix C shell

**zsh - **enhanced shell, similar to korn shell

## Standard commands

**pwd **- print working directory

**~ - **represents users working home path

**environment paths - **variables that allow multiple locations for binaries

* PATH is determined by /etc/profile

**ls **- list contents of directory

* Z = return seLinux context

* a = list hidden files

* l = long listing

* ltr = most recently changed files

**touch **- create or update file

**cp - **copy files

**mv **- move files

**ln **- link files

* s = soft link

**rm** - remove permenantly

**mkdir** - make directory

**rmdir** - remove directory if it’s empty

**find** - looks for files

* find / -name named.conf

* find / -user mary -exec rm {} /;

**locate** - faster than find but only updated once a day

* **updatedb - **ama

**cat** - output to screen

**less/more **- page through output

**head/tail** - grab first/last 10 lines of a file

* -n # = for number of lines

**sort** - by default sorts by alphabetic order

**grep **- searches through file and returns a line

**egrep **- extended grep, includes +, ?, |, ( )

**diff** - compare content

**wc** - word count

**sed (stream editor) **- example bellow

* sed ‘s/Windows/Linux/g’ opsys > newopsys

**awk ** - database manipulation utility

* awk ‘/mike/ {print $1}’ /etc/passwd

**----------------------------------------------Special notes-----------------------------------------------------------**

Information for the info command is in /usr/share/info

Look for docs in /usr/share/doc

**----------------------------------------------------------------------------------------------------------------------------**

## Use grep and regex to analyze a file

**grep options and examples**

* -i = ignore case

* -n = line numbers

* -v = invert match

**syntax for regular expresion**

* . = match any single character

* [ ] = match single character, ie [abc] = b

* [^ ] = invert the above

* ^ = starting position

* $ = ending position

* ( ) = sub-expression

* \n - nth marked sub-expression

* * = matches zero or more times

## Use input/output redirection

**1>filename **- redirect stdout to file "filename"

**1>>filename **- redirect and append stdout to filename

**2>** - redirect stderror to file ‘filename’

**| -** redirect stdout to stdin

**2>&1 - **redirects stderr and stdout

**< - "accept input from a file"**

## Access remote systems using SSH

SSH client configuration in /etc/ssh/ssh_config

* Host * directive (applies to all hosts)

**command** - ssh username@hostname <command>

other tools using ssh

* scp - secure copy over ssh

* sftp - secure ftp

## Access remote systems using VNC

**Steps for setup (as taken from Digital Ocean)**

1. sudo yum install -y tigervnc-server

2. sudo systectml status vncserver@.service

    1. NOTE:each user connecting via VNC will have to start a new instance of the daemon

3. sudo cp /lib/systemd/system/vncserver@.service /etc/systemd/system/vncserver@:4.service

    2. edit the unit file and update the username at the /home/$user portion

4. sudo systemctl daemon-reload

5. sud systemctl enable vncserver@:4.service

6. sudo firewall-cmd --permenat --zone=public --add-port=5904-5905/tcp

7. sudo firewall-cmd --reload

8. ssh user@hostname

9. vncserver

## Login and switch users in multi-users runlevels

**commands** - su $username

* -l = specifies login shell

* - = also login shell

**----------------------------------------------Special notes-----------------------------------------------------------**

/etc/profile is run whenever any user accesses a login shell

**----------------------------------------------------------------------------------------------------------------------------**

## Archive, compress, unpack, and compress files using tar, gzip, and bzip2

**gzip - **Lempel-Ziv algorithm 

**bzip2 **- Burrows - Whelever block

**tar - **archiving utility

* z  = gzip

* j = bzip2

* **NOTE**: tar doesn’t maintain SeLinux but tools such as restorecron preserve settings

**star **- more appropriate for SELinux systems, ie star -xattr -H=exustar -c -f=home.star /home/

* xattr = preserve SELinux

* -H = records the headers for ACLS

## Create and edit text files (vim)

**commands**

* vim $file = open

* / = search

* w or wq = write

* q = close

* q! = abondon changes

* x = deletes highlighted character

* dw = deletes highlighted word

* dd = delete current line

* p = paste 

* y = yank 

* U = restore text

## Create, delete, copy and move files and directories

**cp, mv, rm, mkdir, rmdir**

## Create hard and soft links

**hard-link - **ln /path/to/file /path/to/symlink

* include a copy of the file

* inodes are the same, if on the same partition

**soft-link - **serves as a redirect

## List, set, and change ugo/rwx permissions

**umask - **default file permissions

**chmod** - change permissions of file/dir

**chown ** - change ownership of a file/dir

**chgrp **-  change group of file

**special permission types** 

* **setuid** 

    * execute the file with same permissions as the user who owns the file

    * **chmod u+s $file**

    * **chmod 4**500 **$file** 

* ** setgid**

    * execute with the same permissions as the group

    * **chmod g+s $file**

    * **chmod 2**500 **$file**

* **for both setuid and setgid**

    * **chmod 6**500 **$file**

* **sticky bit**

    * only the owner can remove the file

    * **chmod +t $file**

    * **chmod 1**777** $file**

**ls -l **- list contents in a long format (including permissions)

* lsattr

* chattr +i = immutable (can’t be rm)

* Attributes

    * a - append only (no rm, can append)

    * d - no dump (dissallow backups from dump)

    * e - extend format (set by ext4)

    * i - immutable

    * I - indexed, set on dir for indexing

**Change secondary groups -**

* usermod -G 

    * secondary group

* usermod -g 

    * primary group

Primary group = group that is associated with a created file

## Locate, read, and use system docs including man, info, and files in /usr/share/doc

**man **- gives manual for command

* **man 5 **$file(passwd) = file information

* **mandb**

    * updates man page db

**whatis** - man page search in title

**apropos** - search description

**info - **newer version of man

* opens man page if info page is not found

* **? - **shows navigation options

* **info coreutils**

**/usr/share/doc**

**whereis **- locate binary, configuration, man pages

# Understand and use essential tools

## Boot, reboot, and shutdown a system normally

**Basic management**

* **reboot ** - reboot, systemctl reboot, shutdown -r, init 6

* **shutdown **- halt, systemctl halt, shutdown -h, init 0

    * **shutdown** $time

* **switch off system(power button)**

**Advanced management**

* **suspend - **systemctl suspend

* **hibernate** - systemctl hibernate

* **suspent/hibernate ** - systemctl hybrid-sleep

## Boot systems into different targets manually

**init system**

* **1. single -** maintenance level

* **2**. multi-user without networking** ( **level without network resources (NFS))

* **3**. multi-user without graphical interface

* **5.** multi-user with gui

* The default run level was set in /etc/inittab

* **runlevel **- gets current run level

* **init #** - change to run level X

**systemd**

* **systemctl rescue** - move to single user with mounted local

* **systemctl emergency** - move to single user with only /root mounted

* **systemctl isolate multi-user.target** - move to multi-user without graphical interface

* **systemctl isolate graphical.target** - move to multi-user with a graphical interface

* **systemctl get-default** - get the default run-level

* **systemctl set-default $target** - set the default run-level

**Get dependencies**

* **systemctl list-dependencies $target **- list what services will be started

**Targets**

* multi-user.target

    * not text based

* graphical.target

    * dependencies on a display manager/desktop environment

* emergency.target

    * root command prompt

    * mounts filesystem as read only

* rescue.target

    * single user environment with minimum requirements loaded

* targets can be changed by editing the kernel line in GRUB

    * add systemd.unit=$target 

## Interrupt the boot process in order to gain access to a system

**Option 1 **

1. from the GRUB2 menu type ‘e’ to edit

2. Edit the kernel line (linux16)

    1. add **rd.break** to kernel line

        1. break at an early stage of the boot

    2. add **enforcing= 0** to kernel line

        2. puts SELinux in passive

3. **ctrl -x** to resume the boot process

4. mount /sysroot as read/write

    3. **mount -o remount,rw /sysroot**

5. execute chroot on /sysroot

    4. **chroot /sysroot**

6. change the root password

    5. **passwd root**

    6. **exit**

    7. **exit**

7. connect to server with the root credentials

    8. **restorecron /etc/shadow**

    9. **reboot**

**Option 2**

1. from the GRUB2 menu type ‘e’ to edit

2. append to the end of the kernel line (linux16)

    1. **rd.break**

        1. break at an early stage of the boot

3. **ctrl -x** to resume the boot process

4. mount /sysroot as read/write

    2. **mount -o remount,rw /sysroot**

5. change the root password

    3. **passwd root**

6. re-label for SEcontext at the root directory

    4. **touch /.autorelabel**

## Identify CPU/Memory intensive processes, adjust process priority with renice, and kill processes

**System activities**

* **top **- server activity

* **virt-top** - server activity on a kVM hypervisor

* **ps -def** - process activity

**Finding processes**

* **pgrep** - ps aux | grep

    * ** - l** = list names of processes

**Process priority**

* **-20 most priority, 19 lowest priority**

* nice -n 10 $process/script -start

    * process with a priority of 10

* renice +5 $pid -change priority

    * or renice +5 `pgrep script.sh`

* can testing by using **time**

    * **time nice -n 029 tar -cvf test.tar test.file**

**Process deletion**

* kill $sig $pid

    * kill -l for a list

    * SIGHUP

        * Tells other process 

    * SIGINT

        * "control+c"

    * SIGQUIT

    * SIGKILL

        * can’t block the signal

    * SIGTERM (15)

    * SIGSTOP (19)

    * SIGTSTP (20)

        * a stop that can be ignored

* pkill $string

    * **-u **username

**System Reporting**

* **iostat** - i/o activity

* **netstat** **-i** - network activity

* **netstat -a** - socket activities

* **vmstat 5** - virtual memory activity

* **sar -A** - full report of activities

## Locate and interpret system log files journals

**Presentation **

* most of system logs are in /var/log

    * SELinux - /var/log/audit/audit.log

**Boot process**

* **systemd-analyse** - boot process duration

* **systemd-analyze blame **- time spend in each process

**Journal Analysis**

* **journalctl** - get the contents of the journal

* **journalctl /sbin/crond - ** get all events related to crond

* **journalctl -b ** - get all events since last boot

* **journalctl --since=today ** - events that are from today

* **journalctl -p err - **get events with err priority

* **journalctl -f - **stream events

* **journalctl -n -** last 10 events

    * **-x -** provides additional information

  **Rsyslog.conf**

* provides standard logging configuration

## Access a virtual machine’s console with KVM

**USE THE GUI**

**virsh console hostname**

**Emergency procedure**

* **scenario**: lost all links to virtual machine and didn’t set up a virtual console

* **solution:**

1. Define where the vm image file is located

    1. virsh dumpxml | grep "source file="

2. Map your image file into the host environment

    2. kpartx -av /var/lib/libvirt/images/vm.example.com.img

    3. determine partitions

3. Mount boot partition

4. Edit /mnt/grub2/grub.cfg and add console HySO at the end of every line with the kernel (vmlinuz)

5. Unmount and unmap the vm

    4. unmount /mnt

    5. kpartx -dv /var/lib/libvirt/images/hostname.img

6. Restart the VM

    6. virsh start hostname

7. Connect to the VM

    7. virsh console hostname

## Start and Stop Virtual Machine

**Start a VM**

* virsh start hostname

**Stop a VM**

* virsh shutdown hostname

**Stop a VM immediately**

* virsh destroy hostname

**Delete a VM**

* virsh undefine hostname

**Reboot a VM**

* virsh reboot hostname

**Display configuration information**

* virsh dominfo hostname

**VM Reporting**

* list of all VMs including inactive

    * virsh list --all

* global information of VM activities

    * virt-top

## Start, stop, and check the status of network services

**Basic service management**

* **start a network service**

    * systemctl start service

* **stop a service**

    * systemctl stop service

* **check status of service**

    * systemctl is-active service

* **start service on boot**

    * systemctl enable service

* **check if service is enabled on boot**

    * systemctl is-enabled service

* **to permanently disable service**

    * systemctl mask service

## Securely transfer files between systems

**copy from remote to local**

* scp username@remotehost:file.txt /tmp/file.txt

**copy local file to remote system**

* scp file.txt username@remote:/tmp/file.txt

# Configure local storage

## List, Create, and Delete Partitions on MBR and GPT Disks

**partprobe** - run after update partitions

**gdisk - **GPT tool

**fdisk - **MBR tool

## Create and Remove Physical Volumes, Assign Physical Volumes to Volume Groups, and Create & Delete Logical Volumes

1. Create disk partitions

    1. **fdisk/gdisk /dev/sdX**

        1. **Change label to LVM**

2. Create physical volumes

    2. **pvcreate /dev/disk1 /dev/disk2**

3. Create volume group

    3. **vgcreate vg_name /dev/disk1 /dev/disk2**

4. Create logical volume

    4. **lvcreate -n $lvname -L $size $volumegroup**

5. Make a file system

    5. **mkswap /dev/$volumegroup/$logicalvolume**

6. Verify swap works

    6. swapon **/dev/$volumegroup/$logicalvolume**

## Add New Partitions and Logical Volumes, and Swap to a System Non-Destructively

1. Create disk partition

    1. **fdisk/gdisk /dev/sdX**

2. Create volume group

    2. **vgcreate vg_name /dev/disk1 /dev/disk2**

3. Create logical volume

    3. **lvcreate -n $lvname -L $size $volumegroup**

4. Make a file system

    4. **mkfs.xfs /dev/$volumegroup/$logicalvolume**

## Extend existing un-ecrypted logical volumes

**Note:** only unencrypted Ext4 and XFS can be extended, not VFAT

**To extend**

* lvextend --size +50M -r /dev/vg/lv_vol

**To reduce**

* **unmount the file system**

    * unmount /dev/vg/lv_vol

* **reduce the logical volume and the associated file system at the time (-r)**

    * lvreduce --size -50M -r /dev/vg/lv_vol

* **re-mount the filesystem**

    * mount /dev/vg/lv_vol /mnt

* **(-r) extends the file system automatically**

## Configure Systems to Mount File Systems at Boot by Universally Unique ID (UUID) or Label

**Creating a LABEL**

* **XFS**

    * **xfs_admin -L $name /dev/xfs_partition**

* **EXT4**

    * **tune2fs -L $name /dev/ext4_partition**

**Persistent mount**

* **/etc/fstab**

    * **LABEL=$labelname $mountpoint $fstype $options $dump(0,1) $order**

## Create and configure file systems               

**Ext 4 File System**

* **to create**

    * mkfs.ext4 /dev/vg/lv_vol

* **to mount**

    * mount /dev/vg/lv_vol /mnt

* **mount permanently **

    * edit /etc/fstab

        * add /dev/mapper/vg-lv_vo /mnt ext4 defaults 1 2

* **check an unmounted file systems consistency**

    * fsck /dev/vg/lv_vol

* **to get details about a file system**

    * dumpe2fs /dev/vg/lv_vol

**XFS file system**

* **to create**

    * mkfs.xfs /dev/vg/lv_vol

* **to mount**

    * mount /dev/vg/lv_vol /mnt

* **mount permanently **

    * edit /etc/fstab

        * add /dev/mapper/vg-lv_vo /mnt xfs defaults 1 2

* **check an unmounted file systems consistency**

    * xfs_repair /dev/vg/lv_vol

* **to get details about a file system**

    * xfs_info /dev/vg/lv_vol

**VFat file system**

* **to create**

    * mkfs.vfat /dev/vg/lv_vol

* **to mount**

    * mount /dev/vg/lv_vol /mnt

* **mount permanently **

    * edit /etc/fstab

        * add /dev/mapper/vg-lv_vo /mnt vfat defaults 1 2

* **check an unmounted file systems consistency**

    * fsck.vfat /dev/vg/lv_vol

## Mount and unmount CIFS and NFS network file systems

**Install the NFS client**

* yum install -y nfs-utils

* if not working, add an entry to /etc/hosts

**Activate the nfs-idmap service on boot**

* systemctl enable nfs-idmap

**Start the service **

* systemctl start nfs-idmap

**Permanently mount the share, /etc/fstab**

* nfsserver:/home/tools /mnt nfs4 defaults 0 0

**Execute the /etc/fstab config**

* mount -a

**Check current config**

* mount | grep nfsserver

* if device is busy

    * fuser /mnt

**CIFS network file system**

* **Install the SAMBA client**

    * yum install -y cifs-utils

    * yum install -y samba_client

* **edit /etc/hosts if necessary**

* **edit /etc/fstab**

    * //smbserver/shared /mnt cifs rw,username=$user, password=pass 0 0

* **mount everything**

    * mount -a

* **check current config**

    * mount | grep smbserver

## Extend Existing Logical Volumes

**Extend LG (add a disk)**

* vgextend **$volumegroup** **$physicalvolume**

**Extend a Logical volume**

* lvextend -L **$size** /dev/vg/lv

* lvextend -l +50%FREE /dev/vg/lv

**Move data from one disk to another**

* pvmove /dev/disktomoveoffof

**Updating filesystem**

* xfs_grow /mnt/point

## Create and Configure set-GID Directories for Collaboration

## Configure Networking and Hostname Resolution Statically or Dynamically: Troubleshooting

## Configure Networking and Hostname Resolution Statically or Dynamically: Network Manager

**Check available interfaces**

* ls /sys/class/net

* nmcli dev status

**Network configurations**

* /etc/sysconfig/network-scripts/

**Add a connection**

* nmcli con add con-name "**$connection**" autoconnect yes type ethernet ifname eth1

## Configure Networking and Hostname Resolution Statically or Dynamically: Hostname Configuration

**nsswitch.conf**

* can change what’s looked up first (files, dns, etc)

**setting hostname **

* hostnamectl set-hostname

    * set **permanent** hostname

    * **exec bash** to update

* hostnamectl status

    * hostname information

* **hostname (not persistent)**

**DNS**

* resolv.conf

* nmcli con show

* nmcli con mod **$name** ipv4.dns 

## Start & Stop Services and Configure Services to Start Automatically at Boot

**Check if something is enabled is specific targets**

* systemctl list-dependencies **$target | **grep httpd

**Check if service is enabled in current target**

* systemctl is-enabled **$service**

## Configure Systems to Launch Virtual Machines at Boot

**libvrtd**

* systemctl enable libvrtd

**Configure autostart**

1. virsh

2. autostart $vmname

## Configure Network Services to Start Automatically at Boot

**Ensure NIC is starting at boot**

* /etc/sysconfig/network-scripts

* nmcli con mod **"$name" **connection.autoconnect yes

**Verify services at boot**

* systemctl list-dependencies $target | grep network

## Configure a System to Use Time Services

**timedatectl**

* information about current time 22

**tzselect**

* menu based time zone select

**ntp**

* **chronyd**

    * chronyc sources -v

    * /etc/chrony.conf

        * list of ntp pools

        * **systemctl restart chronyd**

## Install and Update Software Packages from Red Hat Network, a Remote Repository, or From the Local File System: YUM

**Check for updates**

* yum check-update

**Update all of the packages**

* yum update -y

**Search package details and descriptions**

* yum search

## Install and Update Software Packages from Red Hat Network, a Remote Repository, or From the Local File System: RPM

**Downloading a package locally**

* yumdownloader $package

**Install from local file**

* rpm -ivh $filename

* yum localinstall $package

    * manages dependencies

**List installed packages**

* rpm -qa $package

**List files that were unpacked as part of the package**

* rpm -ql $package

**List documentation associated with package**

* rpm -qd $package

## Install and Update Software Packages from Red Hat Network, a Remote Repository, or From the Local File System: Managing Repositories

**.repo files**

* baseurl = URL to repo

**list enabled repositories**

* yum repolist

**disable with yum-config-manager**

* yum-config-manager --disable $**repoid**

**install a repo using yum-config-manager**

* yum-config-manager --add-repo $**repourl**

## Update the Kernel Package Appropriately to Ensure a Bootable System

**Can be done via yum update**

**Using RPM**

* yumdownloader kernel

* rpm -ivh 

**Generate vmlinuz**

* dracut

## Modifying the System Bootloader

* **grub2-set-default #[number of kernels starting at 0]**

## Install and Update Software Packages from Red Hat Network, a Remote Repository, or From the Local File System: Configuring A Local Repository

**From an iso**

* mount -o $**disk.iso**

* create local.repo

    * [local-repo]

    * name=$name

    * baseurl:///path/to/repo

    * enabled=1

    * gpgcheck=0

## Lecture: Install and Update Software Packages from Red Hat Network, a Remote Repository, or From the Local File System: Configuring A Local Repository: Configure The GPG Key

**[http://dl.fedoraproject.org/pub/epel/7/x86_64**/](http://dl.fedoraproject.org/pub/epel/7/x86_64/)

**Get the key**

* wget $**urltogpgkey**

**repo.conf**

* gpgcheck=1

* gpgkey=**$pathtokey**

**Modifying the boot loader**

* Yum list installed kernel 

* Grub2-set-default $kernel

## Create, Delete, and Modify Local User Accounts

**Get information but current user**

* id

    * **uid -**

        * **0 **- root 

        * **1-200  **- redhat** **system user that will own files

        * **201-999**  - system user but don’t own files2

Only one **primary group**

* there are also supplementary groups

**/etc/login.defs**

* some of the defaults for a new user

**/etc/default/useradd**

* defaults when adding a new user

**/etc/shadow**

* hash of password

## Change Passwords and Adjust Password Aging for Local User Accounts

**/etc/passwd**

**view user passwd info**

* chage -l $user

**get date for expirations**

* date -d '+#days' +%F

**change password expirey time**

* chage -E $date $user

**set max number of days**

* chage -M $days $user

## Create, Delete, and Modify Local Groups and Group Memberships

**get group membership of specific group**

* getent group $user

**add user to suplimentary group**

* usermod -G $group $user

**change primary group**

* usermod -g $group $user

**modify group command**

* groupmod -n $newgroup $oldgroup

**assume new group**

* newgrp

##  Using set-GID On Directories

## Configure Key-Based Authentication for SSH

**cached credentials **

* ssh-agent bash

* ssh-add

* ssh username@host command

## Introduction to SELinux

**Introduction**

* if a service such as HTTPD is compromised then the attacker could potentially have access to all open permission files on your system. Essentially, SELinux defines a set of rules that determine what process can access specific files and locations on a file system.

* A context is assigned to every process, directory, and port which is used to determine if a process can access specific resource.

* in this course we will focus on the "type" context

* SELinux **has three modes**

    * **Enabled**

    * **Passive**

    * **Disabled**

        * Disabled must be configured in /etc/selinux/config and a reboot must occur in order to enter into disabled mode. This is not suggested.

* **Boolean:** is a conditional rule that allows runtime modification of the security policy without having to load a new policy. For example, to allow cgi cripts to be executed then you can enable the httpd_enable_cgi boolean. The opposite is true if the adminsitrator wants to just disable all cgi scripsts on a system

* **Man pages:**

    * Booleans (8)

    * Selinux (8)

    * Getsebool (8)

## Set enforcing and permissive modes for SELinux

**Get current selinux mode**

* getenforce

**Set selinux mode**

* setenforce

**Set selinux in disabled mode**

* edit /etc/sysconfig/selinux

## How to Practice and Study After Completing the Course

**Preparing for the exam**:

* Once a day for one to two weeks go through EVERY Linux Academy RHCSA exercise and self-paced lab each day

* Only after you can perform every task without needing the solution are you ready

* Make sure you understand the different situations in which you could use the tools in a given exercises

* The Key is practice

* Memorize the downloadable study guide but also ensure you can perform the tasks on the study guide

## Best Practices to Remember While Taking the Exam

* test takers are bound to an NDA, help students in the community but do not break NDA rules

* If you do not know how to do something use the documentation!

* Go through all the "objectives" and complete what you can first and then come back to the objectives you need to use documentation or man pages to figure out

* When stuck, breathe, and think about how you would normally solve this in a real world environment. All the tools are given to you during the exam in order to complete the objective.

