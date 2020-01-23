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

.

.

[https://developer.ibm.com/tutorials/l-lpic1-104-2/](https://developer.ibm.com/tutorials/l-lpic1-104-2/)

[https://www.thegeekstuff.com/2012/08/fsck-command-examples/](https://www.thegeekstuff.com/2012/08/fsck-command-examples/)

[https://www.computerhope.com/unix/fsck.htm](https://www.computerhope.com/unix/fsck.htm)

[https://www.geeksforgeeks.org/file-system-consistency-checker-fsck/](https://www.geeksforgeeks.org/file-system-consistency-checker-fsck/)

.



