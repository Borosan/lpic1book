# 104.2. Maintain the integrity of filesystems

**Weight:** 2

**Description:** Candidates should be able to maintain a standard filesystem, as well as the extra data associated with a journaling filesystem.

**Key Knowledge Areas:**

* Verify the integrity of filesystems
* Monitor free space and inodes
* Repair simple filesystem problems

**Terms and Utilities:**

* du
* df
* fsck
* e2fsck
* mke2fs
* debugfs
* dumpe2fs
* tune2fs
* XFS tools \(such as xfs\_metadump and xfs\_info\)



In cases when your system crashes or loses power, your filesystems may be left in an inconsistent state, with some changes completed and some not. 

Operating with a damaged filesystem is not a good idea as you are likely to further compound any existing errors.We’ll take a look at  some tools to help us manage such problems.

### fsck

 The main tool for checking and repairing  filesystems is `fsck`, which, like `mkfs`, is really a front end to filesystem-checking routines for the various filesystem types.

```text
root@ubuntu16-1:~# fsck
fsck          fsck.ext3     fsck.fat      fsck.nfs      
fsck.cramfs   fsck.ext4     fsck.minix    fsck.vfat     
fsck.ext2     fsck.ext4dev  fsck.msdos 

root@ubuntu16-1:~# ls /sbin/fsck*
/sbin/fsck         /sbin/fsck.ext3     /sbin/fsck.fat    /sbin/fsck.nfs
/sbin/fsck.cramfs  /sbin/fsck.ext4     /sbin/fsck.minix  /sbin/fsck.vfat
/sbin/fsck.ext2    /sbin/fsck.ext4dev  /sbin/fsck.msdos
```

> Some of these are just links to e2fsck command and they are the same

The fsck command in Linux allows us to manually check for file system inconsistencies,  Fsck command needs to be run with superuser privileges or **root**\(ubuntu 16.04 here\):

```text
root@ubuntu16-1:~# fsck /dev/sda1
fsck from util-linux 2.27.1
e2fsck 1.42.13 (17-May-2015)
/dev/sda1 is mounted.
e2fsck: Cannot continue, aborting.
```

{% hint style="danger" %}
In order to use fsck the partition should be unmounted, otherwise it might cause damages!
{% endhint %}

Lets simply check file system on an unmounted ext3 partition \(sdb1\) and try to fix errors :

```text
root@ubuntu16-1:~# fsck /dev/sdb1
fsck from util-linux 2.27.1
e2fsck 1.42.13 (17-May-2015)
/dev/sdb1: clean, 11/1310720 files, 126322/5242624 blocks
```

 This command will attempt to check **/dev/sdb1**, and report any errors it finds.The exit code returned by fsck is one of following conditions:

* 0 No errors 
* 1 Filesystem errors corrected 
* 2 System should be rebooted 
* 4 Filesystem errors left uncorrected 
* 8 Operational error 
* 16 Usage or syntax error 
* 32 Checking canceled by user request 
* 128 Shared-library error

 `-N` option just shows what would be executed but do not attempt to repair them:

```text
root@ubuntu16-1:~# fsck -N /dev/sdb1
fsck from util-linux 2.27.1
[/sbin/fsck.ext3 (1) -- /dev/sdb1] fsck.ext3 /dev/sdb1
```

 Normally, **fsck** will skip parts of the filesystem marked as "clean" — meaning all pending writes were successfully made. The **-f** \("force"\) option specifies that **fsck** should check parts of the filesystem even if they are not "dirty". The result is a less efficient, but a more thorough check.

```text
root@ubuntu16-1:~# fsck -f /dev/sdb1
fsck from util-linux 2.27.1
e2fsck 1.42.13 (17-May-2015)
Pass 1: Checking inodes, blocks, and sizes
Pass 2: Checking directory structure
Pass 3: Checking directory connectivity
Pass 4: Checking reference counts
Pass 5: Checking group summary information
/dev/sdb1: 11/1310720 files (0.0% non-contiguous), 126322/5242624 blocks

```

> We can also check file systems using their UUID.\(use blkid command \):
>
> ```text
> root@ubuntu16-1:~# blkid /dev/sdb1
> /dev/sdb1: UUID="b9ca56aa-4ea3-4f61-92c8-18c867fd991f" SEC_TYPE="ext2" TYPE="ext3" PARTUUID="4d71bc84-01"
> root@ubuntu16-1:~# fsck -N UUID="b9ca56aa-4ea3-4f61-92c8-18c867fd991f"
> fsck from util-linux 2.27.1
> e2fsck 1.42.13 (17-May-2015)
> /dev/sdb1: clean, 11/1310720 files, 126322/5242624 blocks
> ```

| fsck command example | description |
| :--- | :--- |
| fsck -M /dev/sda1 | prevents  running fsck on mounted filesystem |
| fsck -t ext3 /dev/sdb1 | Check Only a Specific Filesystem Type |
| fsck -y -f /dev/sdb1 | pass “yes” to all the questions to fix |

> For checking  a XFS filesystem, wehave to use xfs\_check command

### tune2fs

 The ext family of filesystems also has a utility called `tune2fs`, which can be used to inspect information about the block count as well as information about whether the filesystem is journaled \(ext3 or ext4\) or not \(ext2\). 

{% hint style="success" %}
The tune2fs command allows you to set assorted filesystem parameters on a mounted ext2 or ext3 filesystem.
{% endhint %}

`-l` shows contents of the filesystem superblock, including the current values of the parameters:

```text
root@ubuntu16-1:~# tune2fs -l /dev/sdb1
tune2fs 1.42.13 (17-May-2015)
Filesystem volume name:   <none>
Last mounted on:          <not available>
Filesystem UUID:          b9ca56aa-4ea3-4f61-92c8-18c867fd991f
Filesystem magic number:  0xEF53
Filesystem revision #:    1 (dynamic)
Filesystem features:      has_journal ext_attr resize_inode dir_index filetype sparse_super large_file
Filesystem flags:         signed_directory_hash 
Default mount options:    user_xattr acl
Filesystem state:         clean
Errors behavior:          Continue
Filesystem OS type:       Linux
Inode count:              1310720
Block count:              5242624
Reserved block count:     262131
Free blocks:              5116302
Free inodes:              1310709
First block:              0
Block size:               4096
Fragment size:            4096
Reserved GDT blocks:      1022
Blocks per group:         32768
Fragments per group:      32768
Inodes per group:         8192
Inode blocks per group:   512
Filesystem created:       Wed Jan 22 23:42:22 2020
Last mount time:          n/a
Last write time:          Thu Jan 23 02:21:07 2020
Mount count:              0
Maximum mount count:      -1
Last checked:             Thu Jan 23 02:21:07 2020
Check interval:           0 (<none>)
Reserved blocks uid:      0 (user root)
Reserved blocks gid:      0 (group root)
First inode:              11
Inode size:	          256
Required extra isize:     28
Desired extra isize:      28
Journal inode:            8
Default directory hash:   half_md4
Directory Hash Seed:      73007ad4-213f-44cd-b8f0-4fbd0c1e90b2
Journal backup:           inode blocks

```

> The command can also be used to set many parameters or convert an ext2 filesystem to ext3 by adding a journal using -j option: `tune2fs -j /dev/sdd1`

Also we can use tune2fs for changing or modifying partition label:

```text
root@ubuntu16-1:~# tune2fs -L myroot /dev/sda1
tune2fs 1.42.13 (17-May-2015)
```

### xfs\_info

xfs file system has it's own family commands. xfs\_info is the same as tune2fs but for xfs.

{% hint style="success" %}
xfs\_info should be used on a mounted file system.
{% endhint %}

> xfsprogs package must be installed

```text
root@ubuntu16-1:~# xfs_info /dev/sdb1
meta-data=/dev/sdb1              isize=512    agcount=4, agsize=1310656 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=1        finobt=1 spinodes=0
data     =                       bsize=4096   blocks=5242624, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
log      =internal               bsize=4096   blocks=2560, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
```

| xfs command | description |
| :--- | :--- |
| xfs\_info | shows information |
| xfs\_check | complete check of file system |
| xfs\_repair | check and fixes problems |
| xfs\_check -n | same as xfs\_check |

```text
root@ubuntu16-1:~# xfs_repair -n /dev/sdb1
xfs_repair: /dev/sdb1 contains a mounted and writable filesystem

fatal error -- couldn't initialize XFS library
```

{% hint style="danger" %}
obviously xfs\_repair does not work on mounted file system.
{% endhint %}

```text
root@ubuntu16-1:~# xfs_repair  /dev/sdb1
Phase 1 - find and verify superblock...
Phase 2 - using internal log
        - zero log...
        - scan filesystem freespace and inode maps...
        - found root inode chunk
Phase 3 - for each AG...
        - scan and clear agi unlinked lists...
        - process known inodes and perform inode discovery...
        - agno = 0
        - agno = 1
        - agno = 2
        - agno = 3
        - process newly discovered inodes...
Phase 4 - check for duplicate blocks...
        - setting up duplicate extent list...
        - check for inodes claiming duplicate blocks...
        - agno = 0
        - agno = 1
        - agno = 2
        - agno = 3
Phase 5 - rebuild AG headers and trees...
        - reset superblock...
Phase 6 - check inode connectivity...
        - resetting contents of realtime bitmap and summary inodes
        - traversing filesystem ...
        - traversal finished ...
        - moving disconnected inodes to lost+found ...
Phase 7 - verify and correct link counts...
done
```

> In XFS, you can only extend file system and can not reduce it.

{% hint style="info" %}
**Super Blocks**

 You may be wondering how all these checking and repairing tools know where to start. Linux and UNIX filesystems usually have a _superblock_, which describes the filesystem _metadata_, or data describing the filesystem itself. This is usually stored at a known location, frequently at or near the beginning of the filesystem, and replicated at other well-known locations. You can use the `-n` option of `mke2fs` to display the superblock locations for an existing filesystem.
{% endhint %}

### Monitoring free space <a id="monitoring-free-space"></a>

 On a storage device, a file or directory is contained in a collection of _blocks_. Information about a file is contained in an _inode._

> inodes keeps information such as who the owner is, when the file was last accessed, how large it is, whether it is a directory, and who can read from or write to it. The inode number is also known as the file serial number and is unique within a particular filesystem.

Data blocks and inodes each take space on a filesystem, so we need to monitor the space usage to ensure that your filesystems have space for growth.

### df

The `df`  \(DiskFree\) command is used to find out about the free and used space of file systems.

```text
df [OPTION]...[FILE]...
```

 If no file name is passed as an argument with df command then it shows the space available on all currently **mounted** file systems

```text
root@ubuntu16-1:~# df
Filesystem     1K-blocks    Used Available Use% Mounted on
udev              462796       0    462796   0% /dev
tmpfs              98508    6544     91964   7% /run
/dev/sda1       50442940 5613840  42243708  12% /
tmpfs             492540     284    492256   1% /dev/shm
tmpfs               5120       4      5116   1% /run/lock
tmpfs             492540       0    492540   0% /sys/fs/cgroup
tmpfs              98508      48     98460   1% /run/user/1001
```

-T  print file system type,  -h, –human-readable  ****print sizes \(in power of 1024\):

```text
root@ubuntu16-1:~# df -Th
Filesystem     Type      Size  Used Avail Use% Mounted on
udev           devtmpfs  452M     0  452M   0% /dev
tmpfs          tmpfs      97M  6.4M   90M   7% /run
/dev/sda1      ext4       49G  5.4G   41G  12% /
tmpfs          tmpfs     481M  284K  481M   1% /dev/shm
tmpfs          tmpfs     5.0M  4.0K  5.0M   1% /run/lock
tmpfs          tmpfs     481M     0  481M   0% /sys/fs/cgroup
tmpfs          tmpfs      97M   48K   97M   1% /run/user/1001
```

> `-H` make numbers human readable also  \(in powers of 1000\).

-i list inode information instead of block usage:

```text
root@ubuntu16-1:~# df -i
Filesystem      Inodes  IUsed   IFree IUse% Mounted on
udev            115699    456  115243    1% /dev
tmpfs           123135    750  122385    1% /run
/dev/sda1      3211264 256212 2955052    8% /
tmpfs           123135      9  123126    1% /dev/shm
tmpfs           123135      6  123129    1% /run/lock
tmpfs           123135     17  123118    1% /sys/fs/cgroup
tmpfs           123135     27  123108    1% /run/user/1001
```

> Remember?  there is no owner or access rights on vfat filesystems. vfat file format has no inodes!

| df command example | description |
| :--- | :--- |
| df -a  |  dislpay all information includes pseudo, duplicate and inaccessible file systems. |
| df -Th /home | Display Information of /home File System |
| df -k or -m or -h | displays information in Bytes, MB , GB |
| df -t ext3 | include specific file system type |
| df -x xfs | exclude specific file system type |

 The `df` command gives information about a whole filesystem. Sometimes you might want to know how much space is used by a specific file or  directory, To answer this kind of question, we  use the `du` command.

### du

 The Linux `du` \(Disk Usage\)  command, used to check the information of **disk usage of files and directories** on a machine.

```text
du [OPTION]... [FILE]...
```

| useful switch | description |
| :--- | :--- |
| -a | write count of all files, not just directories |
| -h  |  human readable Means we can see sizes in Bytes, KB, MB, GB,... |
| -c | grand total usage disk space at the last line |
|  –max-depth=N | go N or fewer sub directories further |
| -s | display only total for each directory |

```text
root@ubuntu16-1:~# du
16	./.aptitude
8	./.cache/dconf
12	./.cache
8	./.config/htop
8	./.config/gedit
8	./.config/dconf
4	./.config/ibus/bus
8	./.config/ibus
36	./.config
4	./.gnupg/private-keys-v1.d
48	./.gnupg
4	./.gconf
12	./.elinks
16	./.ssh
4	./.nano
8	./.local/share
12	./.local
336	./backup
8	./.dbus/session-bus
12	./.dbus
4	./test-space
932	.

root@ubuntu16-1:~# du -sh
932K	.
```

**--time** option is used to display the last modification time in the output of du.

**--exclude=PATTERN** will exclude files that match PATTERN example: `du -ah --exclude="*.txt" /home/payam`



.

.

[https://developer.ibm.com/tutorials/l-lpic1-104-2/](https://developer.ibm.com/tutorials/l-lpic1-104-2/)

[https://www.thegeekstuff.com/2012/08/fsck-command-examples/](https://www.thegeekstuff.com/2012/08/fsck-command-examples/)

[https://www.computerhope.com/unix/fsck.htm](https://www.computerhope.com/unix/fsck.htm)

[https://www.geeksforgeeks.org/file-system-consistency-checker-fsck/](https://www.geeksforgeeks.org/file-system-consistency-checker-fsck/)[https://www.serverwatch.com/tutorials/article.php/3797306/tune2fs-Makes-It-Easy-to-Play-With-Filesystems.htm](https://www.serverwatch.com/tutorials/article.php/3797306/tune2fs-Makes-It-Easy-to-Play-With-Filesystems.htm)[https://jadi.gitbooks.io/lpic1/content/1042\_maintain\_the\_integrity\_of\_filesystems.html](https://jadi.gitbooks.io/lpic1/content/1042_maintain_the_integrity_of_filesystems.html)

[https://www.geeksforgeeks.org/df-command-in-linux-with-examples/](https://www.geeksforgeeks.org/df-command-in-linux-with-examples/)

[https://www.geeksforgeeks.org/df-command-linux-examples/](https://www.geeksforgeeks.org/df-command-linux-examples/)

[https://www.tecmint.com/how-to-check-disk-space-in-linux/](https://www.tecmint.com/how-to-check-disk-space-in-linux/)

[https://www.geeksforgeeks.org/du-command-linux/](https://www.geeksforgeeks.org/du-command-linux/)

[https://www.geeksforgeeks.org/du-command-linux-examples/](https://www.geeksforgeeks.org/du-command-linux-examples/)

[https://www.tecmint.com/check-linux-disk-usage-of-files-and-directories/](https://www.tecmint.com/check-linux-disk-usage-of-files-and-directories/)

With the special thanks of shawn powers.

.

.



