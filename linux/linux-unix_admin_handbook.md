**The UNIX Admin Handbook**

**Where to start**

* Performing backups is perhaps the most important job of the system admin, and its also the job that is most often ignored.

* It is also recommended to learn **expect** which is not a programming language so much as a front end for driving interactive programs

* Linux has additional information and references at tldb.org

* **Locate** is part of the GNU **findutils** package which is included by default on most Linux systems but must be installed by hand on Unix. 

**Scripting and the shell**

* Administrative scripts should emphasize programmer efficiency and code clarity rather than computational efficiency. 

* STDIN, STDOUT, and STDERR are guaranteed to correspond to file descriptors 0, 1, 2

* To discard an error message use 2> /dev/null

* Command-line arguments to a script become variables whose names are numbers. $1 is the first command-line argument, $2 is the second and so on. $0 is the name by which the script was invoked.

* The variable $# contains the number of command line arguments that were supplied

* $* contains all of the arguments excluding $0

* Bash is great at evaluating using legacy /usr/bin/**test**

**Booting and shutting down**

* The kernel probes the systems hardware and then spawns the system's **init** process which is always process 1

* A typical bootstrapping process

    * Reading of the boot loader from the MBR

    * Loading and initialization of the kernel

    * Device detection and configuration

    * Creation of kernel process

    * Administrator intervention (single-user)

    * Execution of system startup scripts.

* The exact number of spontaneous processes varies although **init**** **is always PID 1. Most UNIX systems have **sched**** **as process 0

* Low PID processes will often display as [process/0]

    * The number is which CPU the task is on

    * Some processes

        * **kjournal d**: commits fs journal updates to disk

        * **Kswapd:** swaps processes when memory is low

        * **Ksoftirq**: handles soft interrupts

        * **Khubd**: configures USB devices

* RedHat’s single-user mode is more aggressive. It tries to mount local filesystem in readonly

* When a machine boots, it begins b executing code stored in ROM.

    * On PC’s the initial boot-code is the BIOS

    * Once BIOS selects the boot device, it tries to read the first block of the device

        * This 512-byte block is the MBR.

    * The default MBR contains a simple program that tells the computer to get its boot loader from the first partiiton of the disk.

    * The loader is then responsible for loading the kernel

**GRUB(Grand Unified Boot Loader)**

* GRUB’s job is to grab a kernel from the previously assembled list and to load that kernel with options specified by the administrator

* By default, GRUB reads its default boot config from /boot/grub/menu.lst or /boot/grub/grub.conf

* The **chainloader**** **menu config option specifies where the boot loader should be loaded from

**Startup Scripts**

* Sysv-init

    * Scripts are kept in /etc/initd and links to them are made in directories /etc/rc0.d, /etc/rc1.d, and so on (run-level)

    * Tasks often performed in those scripts

        * Setting the name of the machine

        * Setting the time-zone

        * Checking the disks with fsk

        * Mounting the systems disk

        * Removing old files from the /tmp dir

        * Configuring network interfaces

        * Starting up daemons and network services

* Init

    * Defiines at least seven run-levels

        * 0 - shutdown

        * 1 - single user mode

        * 2-5 - networking support

        * 6 - reboot level

    * Run level 5 is used for X windows.

**Access control and rootly powers**

**Traditional UNIX access control**

* Objects (e.g, files and processes) have owners. Owners have broad (but not unrestricted) controls over their objects

* You own the objects you create

* The special user account "root" can act as the owner of any object

* Only root can perform certain sensitive administrative operations.

* UIDs map to /etc/passwd; GIDs map to /etc/group

**Process ownership**

* The owner of a process can send the process signals and can also reduce the priority (nice) 

* Process have multiple identities

    * A real, effective, and saved UID

    * A filesystem UID that is used only to determine file access permissions

    * Real numbers are used for accounting effective numbers are used for access permission

**Root account**

* An example of superuser powers is the ability of a process owned by root to change its UID and GID

* Login program as an example

    * If you properly type your username and password, the login program changes its UID and GID to your corresponding IDs and then changes its environment

**Modern Access Control**

* Some examples include PAM

**Role based Access Control**

* The idea is to add a layer of indirection to access control calculations

* Instead of permissions being assigned directly to users, they are assigned to intermediate constructs known as "roles" and roles in turn are assigned to users

* SELINUX (Security Enhanced Linux)

    * Project that has been freely available since late 2000

    * Primary purpose is to enable mandatory access control aka MAC

* POSIX capabilities

    * Systems are capable of subdividing the privileges of the root account according to the POSIX standard for "capabilities"

**PAM (pluggable authentication modules)**

* An authentication technology rather than an access control technology.

**Traditional UNIX Access Control**

* General rules that shaped design

    * Objects (files and process)

    * You own objects you create

    * Root can act as owner of any object

    * Only root can perform certain actions

* **Modern Access Control**

    * Role-based access control

        * Can be used outside of the context of a file system

        * Can also have relationships

            * Such as Admins are X, Y, and Z

    * Solaris Role Based control

        * /etc/group

        * /etc/security/auth_attr - authorizations

        * /etc/secrutiyprofile_attr - profiles

        * /etc/usr/attrs - bindings for the profiles, users, and auth

    * HP and AIX also support Roles

        * Hpux.something and aix.something

    * SELinux

        * Mandatory access control

        * Access is delegated by adminstrator

    * PAM: pluggable authentication module

        * How we know user is (x)

    * Kerberos

        * Thid party cryptographic authentication

        * A specific authentication method

        * PAM = wrapper; Kerberos = method

        * Uses a third party server for verification

    * Access control lists

        * Most systems support these

* **Real-world access control**

    * In DES encryption, only the first 8 characters matter for passwords

    * Type full path to ‘**su**’ as in /bin/su

    * Sudo advantages

        * Accountability is much improved

        * Operators can do chores without unlimited root

        * The real root password isn’t given out

        * Privileges can be revoked

        * A list of admin user is maintained

        * No shared logins

**Controlling processes**

* A process consists of

    * An address space

    * Set of data structures

    * Because UNIX and Linux are virtual memory systems there is no correlation between the physical space and the address space

    * Kernel data structures record

        * The process address space map

        * Status of the process

        * Execution priority

        * Resource usage

        * Files and ports opened by process

        * Owner of the process

* Use ps lax for process info (doesn’t translate UID)

**The Filesystem**

* The filesystem can be thought of as comprising four main components

    * A namespace

        * A way to name things and organize them in a hierarchy

    * An API

        * A set of system calls for navigating and manipulating objects

    * A security model

        * A scheme for protecting, hiding, and sharing things

    * An implementation

        * Software to be the logical model to the hardware

* Instead of reaching for **unmount -f **when a filesystem you’re trying to unmount turns out to be busy, use **fuser** to find which process holds references to the filesystem

* Character and block device files

    * Character device files allow their associated drivers to do their own input and output buffering. 

    * Block device files are used by drivers that handle IO in large chunks and want the kernel to perform buffering for them.

    * In the past, a few types of hardware were represented by both block and character device files, but that configuration is unusual today.

**Local Domain Sockets**

* Sockets are connections between processes that allow processes to communicate hygienically. 

* UNIX defines several kinds of sockets, most of which involve the use of a network. Local domain sockets are accessible only from the local host and are referred to through a filesystem object rather than a network port. 

* Local domain sockets are created with the socket system call and removed with the rm command or the unlink system call once they have no more users.

**Named Pipes**

* Like local domain sockets, named pipes allow communication between two processes running on the same host

* Named pipes and local domain sockets serve similar purposes, and the fact that both exist is essentially a history artifact. Neither of them would exist if UNIX and Linux were designed today; **network sockets would stand in for both.**

**File attributes**

* Sticky bit

    * If the sticky bit is set on a directory, the file system won’t allow you to delete or rename a file unless you are the owner of the directory, the owner of the file, or the superuser.

    * Having write permission on the directory is not enough. This convention helps make directories like /tmp a little more private and secure. 

* Inode number

    * The inode number is an index into a table that enumerates all the files in the filesystem. Inodes are things that are pointed to by directory entities

        * Entities that are hard links to the same file have the same inode number.

**Access Control Lists**

* Because ACL implementations are filesystem specific and because systems support multiple file system implementations, many systems nud up supporting multiple types of ACLs. Even a given filesystem may offer several ACL options, as ni IBM’s JFS2. 

* If multiple ACL systems are available, the commands to manipulate them might be the same or different; it depends on the system. 

* ACL mask

    * The mask specifies an upper bound on the access that the ACL can assign to individual groups and users.

    * It is conceptually similar to the **umask**, except that ACL mask is always in effect and specifies the allowed permissions rather than the permissions to be denied.

    * ACL entries for named users, named groups, and the default group can include permission bits that are not present in the mask, but the kernel simply ignores them.

    * As a result, the traditional mode bits can never underestimate the access allowed by the ACL as a whole. Furthermore, clearing a bit from the group portion of the traditional mode clears the corresponding bit in the ACL mask and thereby forbids the permission to everyone but the file’s owner and those who fall in the category of other.

* Access determination

    * When a process attempts to access a file, its effective UID is compared to the UID that owns the file.

    * If they are the same, access is determined by the ACL’s user:: permissions. 

    * Otherwise, if a matching user-specified ACL entry exists, permissions are determined by that entry in combination with the ACL mask.

    * If no user-specific entry is available, the file system tries to locate a valid group-related entry that provides the requested access; these entries are processed in conjunction with the ACL mask. 

    * If no matching entry can be found, the other:: entry prevails. 

* You can obtain a tabular display of ACL entries with **ls -V**. In this mode, permissions are represented by their one-letter codes. All possible bits are displayed for each access control entry.

    * Those that are turned off are represented by dashes

**Adding New Users**

* Password encryption

    * MD5 passwords are easy to spot because they always start with $1$ or $md5$. 

    * Blowfish passwords start with $2a$ prefix

    * SUSE defaults to Blowfish encryption for new passwords, which is a very reasonable default.

    * OpenSolaris now defaults to SHA256 (prefix $5$), although previous versions used MD5 by default

* Editing the Passwd and Group Files

    * If you have to add a user by hand, use **vipw** to edit the **passwd** and **shadow** files.

* Setting A Password

    * Some automated systems for adding new users do not require you to provide an initial password. Instead, they force the user to set a password on first login. Although this feature is convenient, it’s a giant security hole: anyone who can guess new login names (or look them up in /etc/passwd) can swoop down and hijack the accounts before the intended users have had a chance to log in.

* Umask

    * Be sure to set a reasonable default value for **umask**, we suggest 077, 027, or 022, depending on the friendliness and size of your site.

    * If you do not use individual groups, we recommend umask 077 because it gives the owner full access but the group and the rest of the world no access

* Final steps for user creation

    * Remind new users to change their passwords immediately.

    * If you wish, you can enforce this by setting the password to expire within a short time.

    * Another option is to have a script check up on new users and be sure their encrypted passwords in the **shadow** file have changed.

* **/etc/security/login.cfg**

    * Contains parameters to control bad logins

        * The delay inserted between prompts for the login and password

        * Times at which login is allowed

        * Number of bad logins before the account is disabled

        * List of valid shells

* Disabling Logins

    * Linux

        * Usermod -L *user *(lock)

        * Usermod -U *user *(unlock)

        * Put a star or some other character in front of the user’s encrypted password in /etc/shadow

* Reducing Risk with PAM

    * Centralize the management of the system’s authentication facilities through library routines so that programs like **login, sudo, passwd, **and **su** do not have to supply their own authentication code. 

* Centralizing account management

    * 1

