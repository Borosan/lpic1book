# 104.7. Find system files and place files in the correct location

**Weight:** 2

**Description: **Candidates should be thoroughly familiar with the Filesystem Hierarchy Standard (FHS), including typical file locations and directory classifications.

**Key Knowledge Areas:**

* Understand the correct locations of files under the FHS
* Find files and commands on a Linux system
* Know the location and purpose of important file and directories as defined in the FHS

**Terms and Utilities:**

* find
* locate
* updatedb
* whereis
* which
* type
* /etc/updatedb.conf

There are  over 200 Linux distributions available , all having a lot of things in common, there should be a standard way to place files in the system, and that's where FHS comes to play.

### FHS

 The Filesystem Hierarchy Standard (FHS) is a document that specifies a common layout of directories on a Linux/ UNIX system.

| Directory | Purpose                                                |
| --------- | ------------------------------------------------------ |
| bin       | Essential command binaries                             |
| boot      | Static files of the boot loader                        |
| dev       | Device files                                           |
| etc       | Host-specific system configuration                     |
| lib       | Essential shared libraries and kernel modules          |
| media     | Mount point for removable media                        |
| mnt       | Mount point for mounting a filesystem temporarily      |
| opt       | Add-on application software packages                   |
| sbin      | Essential system binaries                              |
| srv       | Data for services provided by this system              |
| tmp       | Temporary files                                        |
| usr       | Secondary hierarchy                                    |
| var       | Variable data                                          |
| home      | User home directories (optional)                       |
| lib       | Alternate format essential shared libraries (optional) |
| root      | Home directory for the root user (optional)            |

{% hint style="info" %}
The `/usr` and `/var` hierarchies are complex enough to have complete sections of the FHS devoted to them.

The`/usr`Contains binaries, libraries, documentation, and source-code for second level programs.

* **/usr/bin** contains binary files for user programs.
* **/usr/sbin** contains binary files for system administrators.
* **/usr/lib** contains libraries for /usr/bin and /usr/sbin
* **/usr/local** contains users programs that you install from source.
* **/usr/src** holds the Linux kernel sources, header-files and documentation.

The `/var` filesystem contains variable data files, including spool directories and files, administrative and logging data, and transient and temporary files. Some portions of `/var` are not shareable between different systems, but others, such as `/var/mail`, `/var/cache/man`, `/var/cache/fonts`, and `/var/spool/news`, may be shared.
{% endhint %}

### PATH

When you run a program at the command line, the bash (or other) shell searches through a list of directories to find the program you requested. The list of directories is specified in your PATH environment variable.

```
root@ubuntu16-1:~# echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin
```

```
user1@ubuntu16-1:~$ echo $PATH
/home/user1/bin:/home/user1/.local/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
```

As you can see, the PATH variable is just a list of directory names, separated by colons. Note the differences between the user path and root path

As we said in previous sections we can change our path with export `PATH=$PATH:/new/path/dir `or by adding thant inside **.bash_profile** or **.bashrc** .

#### which, type, and whereis

### which

The `which `command to search your path and find out which command will be executed (if any) when you type a command.

```
root@ubuntu16-1:~# which ping
/bin/ping
```

>  The `which` command shows you the first occurrence of a command in your path. If you want to know if there are multiple occurrences, then add the `-a` option

### type

 There are some commands that the `which` command will not find, such as shell builtins.

```
root@ubuntu16-1:~# which for
root@ubuntu16-1:~# type for
for is a shell keyword
root@ubuntu16-1:~# type type
type is a shell builtin
```

 The `type` command is a builtin that understand bash keywords and can tell us how a given command string will be evaluated for execution.

### whereis

 If you want more information than just the location of a program, you can use the `whereis` command.

```
root@ubuntu16-1:~# whereis ping
ping: /bin/ping /usr/share/man/man8/ping.8.gz

root@ubuntu16-1:~# whereis mkfs
mkfs: /sbin/mkfs.ext4dev /sbin/mkfs /sbin/mkfs.minix /sbin/mkfs.ext2 /sbin/mkfs.ext4 /sbin/mkfs.ext3 /sbin/mkfs.fat /sbin/mkfs.bfs /sbin/mkfs.cramfs /sbin/mkfs.ntfs /sbin/mkfs.vfat /sbin/mkfs.msdos /usr/share/man/man8/mkfs.8.gz
```

 The `whereis` command can also search for man pages and source codes of programs alongside their binary location .

### find

In an earlier tutorial in this series "104-7", you learned how to find files based on name (including wildcards), path, size, or timestamp. In another earlier tutorial in this series, “104-6” you learned how to find the links to a particular file or inode.

The `find` command is the Swiss Army knife of file-searching tools on Linux systems. Two other capabilities that you may find useful are its ability to find files based on user or group name and its ability to find files based on permissions.

#### Finding by Owner and Permissions

We can also search for files by the file owner or group owner (discussed in "104-5"). We do this by using the `-user` and `-group` parameters respectively.

```
### finding bu user
root@ubuntu16-1:~# find /tmp -user user1 | head
/tmp/gnome-software-ZPJTE0
/tmp/gnome-software-ZPJTE0/debconf.socket
/tmp/gnome-software-YDX3E0
/tmp/gnome-software-YDX3E0/debconf.socket
/tmp/gnome-software-GD8NE0

### finding by group
root@ubuntu16-1:~/test-space# find /etc  -group shadow
/etc/shadow
/etc/gshadow
```

> use -nouser or -nogroup to search for a file with no user or with no group id.

We can also search for files with specific permissions. If we want to match an exact set of permission `find / -perm 644`

 If we want to specify anything with at least those permissions:`find / -perm -644` .

#### filtering by depth

We can specify the maximum depth of the search under the top-level search directory:

```
find -maxdepth num -name query
```

```
find -mindepth num -name query
```

Also it is possible to combine the min and max depth parameters to focus in on a narrow range` find -mindepth num -maxdepth num -name file`:

```

root@ubuntu16-1:~# find /tmp  -mindepth 1 -maxdepth 2 -user user1 | head
/tmp/gnome-software-ZPJTE0
/tmp/gnome-software-ZPJTE0/debconf.socket
/tmp/gnome-software-YDX3E0
/tmp/gnome-software-YDX3E0/debconf.socket
/tmp/gnome-software-GD8NE0
/tmp/gnome-software-GD8NE0/debconf.socket
/tmp/gnome-software-U5COF0
/tmp/gnome-software-U5COF0/debconf.socket
```

> Like other tests, you can add a ! just before any phrase to negate it. So this will find files not belonging to user1 : `find . ! -user user1`

#### locate & updatedb

 The `find` command searches all the directories you specify, every time you run it. To speed things up, you can use another command, `locate`, which uses a database of stored path information rather than searching the filesystem every time.

### locate

 The `locate` command searches for matching files in a database that is usually updated daily (by cron job).

```
root@ubuntu16-1:~# locate bin/ls
/bin/ls
/bin/lsblk
/bin/lsmod
/sbin/lsmod
/sbin/lspcmcia
/usr/bin/lsattr
/usr/bin/lsb_release
/usr/bin/lscpu
/usr/bin/lsdiff
/usr/bin/lshw
/usr/bin/lsinitramfs
/usr/bin/lsipc
/usr/bin/lslocks
/usr/bin/lslogins
/usr/bin/lsof
/usr/bin/lspci
/usr/bin/lspgpot
/usr/bin/lsusb
/usr/lib/klibc/bin/ls
/usr/lib/plainbox-provider-checkbox/bin/lsmod_info
/usr/lib/plainbox-provider-resource-generic/bin/lsb_resource
```

 The `locate` command matches against any part of a path name, not just the file name.

### updatedb

 The default database used by locate is stored in the` /var` filesystem, in a location such as `/var/lib/locatedb`. _This may be different on systems that use slocate or mlocate packages to provide additional security or speed_. You can find statistics on your locate database using locate-S :

```
root@ubuntu16-1:~# locate -S
Database /var/lib/mlocate/mlocate.db:
	25,916 directories
	251,926 files
	13,669,511 bytes in file names
	6,067,728 bytes used to store database
```

 The database is created or updated using the `updatedb` command. `(`This is usually run daily as a cron job).

```
root@ubuntu16-1:~# updatedb
```

> use -v for verbose mode to see what is going on after updatedb command!

The file `/etc/updatedb.conf`, or sometimes` /etc/sysconfig/locate`, is the configuration file for `updatedb:`

```
PRUNE_BIND_MOUNTS="yes"
# PRUNENAMES=".git .bzr .hg .svn"
PRUNEPATHS="/tmp /var/spool /media /home/.ecryptfs /var/lib/schroot"
PRUNEFS="NFS nfs nfs4 rpc_pipefs afs binfmt_misc proc smbfs autofs iso9660 ncpfs coda devpts ftpfs devfs mfs shfs sysfs cifs lustre tmpfs usbfs udf fuse.glusterfs fuse.sshfs curlftpfs ecryptfs fusesmb devtmpfs"
```

There are some PRUNING on the configuration file which cause locate never search for those kinds of files or directories like` /tmp`  or` /var/spool` . You can let locate to search for them too if you like by manipulating this file.

that's all.

## Congratulation we have done lpic1-101 !!!

.

.

.

[https://developer.ibm.com/tutorials/l-lpic1-104-7/](https://developer.ibm.com/tutorials/l-lpic1-104-7/)

[https://www.geeksforgeeks.org/linux-file-hierarchy-structure/](https://www.geeksforgeeks.org/linux-file-hierarchy-structure/)

[https://www.digitalocean.com/community/tutorials/how-to-use-find-and-locate-to-search-for-files-on-a-linux-vps](https://www.digitalocean.com/community/tutorials/how-to-use-find-and-locate-to-search-for-files-on-a-linux-vps)[https://jadi.gitbooks.io/lpic1/content/1047\_find_system_files_and_place_files_in_the_correct_location.html](https://jadi.gitbooks.io/lpic1/content/1047\_find_system_files_and_place_files_in_the_correct_location.html)

.
