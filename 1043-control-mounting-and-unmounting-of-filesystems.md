# 104.3. Control mounting and unmounting of filesystems

**Weight: **3

**Description: **Candidates should be able to configure the mounting of a filesystem.

**Key Knowledge Areas:**

* Manually mount and unmount filesystems
* Configure filesystem mounting on bootup
* Configure user mountable removable filesystems

**Terms and Utilities:**

* /etc/fstab
* /media/
* mount
* umount

In previous lessons we have learn how to partition hard drives  and how to format file system on to those partitions. In this section we will learn how to mount and unmount  files systems so we can access them, store and retrieves  files from the actual hard drive.

### Linux filesystems <a href="linux-filesystems" id="linux-filesystems"></a>

The Linux filesystem is one big tree rooted at /, and yet we have filesystems on different devices and partitions. How do we resolve this apparent incompatibility?

 The root (/) filesystem is mounted as part of the initialization process. Each of the other filesystems that you create is not usable by Linux system until it is _mounted_ at a _mount point_.

## mounting and unmounting

 **mount** command is used to mount the filesystem found on a device to big tree structure(**Linux** filesystem) rooted at ‘**/**‘.  Conversely, another command **umount** can be used to detach these devices from the Tree.

### mount

```
mount -t type device location
```

> Actually the place we specify as location is mount point. mount point should be exist other wise the device could not be mounted.

With -t option we can specify file system type, how ever it can be omitted as mount command is smart enough to determine file system type!(ubuntu 16.4):

Lets create a directory called "mydisk" as a mount point and mount /dev/sdb1 on that:

```
root@ubuntu16-1:/mnt# pwd
/mnt

root@ubuntu16-1:/mnt# mkdir mydisk

root@ubuntu16-1:/mnt# mount -t ext4 /dev/sdb1 /mnt/mydisk

root@ubuntu16-1:/mnt# cd mydisk/

root@ubuntu16-1:~/mydisk# ls
file1.txt  lost+found

root@ubuntu16-1:/mnt/mydisk# cat file1.txt 
 this text file is on my hard disk!

```

{% hint style="info" %}
When you mount a filesystem over an existing directory, the files on the filesystem you are mounting become the files and subdirectories of the mount point. If the mount point directory already contained files or subdirectories, they are not lost, but are no longer visible until the mounted filesystem is unmounted, at which point they become visible again. It is a good idea to avoid this problem by using only empty directories as mount points.
{% endhint %}

mount command without any arguments  displays all the mounts on the system:

```
root@ubuntu16-1:~# mount
sysfs on /sys type sysfs (rw,nosuid,nodev,noexec,relatime)
proc on /proc type proc (rw,nosuid,nodev,noexec,relatime)
udev on /dev type devtmpfs (rw,nosuid,relatime,size=462796k,nr_inodes=115699,mode=755)
devpts on /dev/pts type devpts (rw,nosuid,noexec,relatime,gid=5,mode=620,ptmxmode=000)
tmpfs on /run type tmpfs (rw,nosuid,noexec,relatime,size=98508k,mode=755)
/dev/sda1 on / type ext4 (rw,relatime,errors=remount-ro,data=ordered)
securityfs on /sys/kernel/security type securityfs (rw,nosuid,nodev,noexec,relatime)
tmpfs on /dev/shm type tmpfs (rw,nosuid,nodev)
tmpfs on /run/lock type tmpfs (rw,nosuid,nodev,noexec,relatime,size=5120k)
tmpfs on /sys/fs/cgroup type tmpfs (ro,nosuid,nodev,noexec,mode=755)
cgroup on /sys/fs/cgroup/systemd type cgroup (rw,nosuid,nodev,noexec,relatime,xattr,release_agent=/lib/systemd/systemd-cgroups-agent,name=systemd)
pstore on /sys/fs/pstore type pstore (rw,nosuid,nodev,noexec,relatime)
cgroup on /sys/fs/cgroup/pids type cgroup (rw,nosuid,nodev,noexec,relatime,pids)
cgroup on /sys/fs/cgroup/freezer type cgroup (rw,nosuid,nodev,noexec,relatime,freezer)
cgroup on /sys/fs/cgroup/cpu,cpuacct type cgroup (rw,nosuid,nodev,noexec,relatime,cpu,cpuacct)
cgroup on /sys/fs/cgroup/net_cls,net_prio type cgroup (rw,nosuid,nodev,noexec,relatime,net_cls,net_prio)
cgroup on /sys/fs/cgroup/blkio type cgroup (rw,nosuid,nodev,noexec,relatime,blkio)
cgroup on /sys/fs/cgroup/devices type cgroup (rw,nosuid,nodev,noexec,relatime,devices)
cgroup on /sys/fs/cgroup/hugetlb type cgroup (rw,nosuid,nodev,noexec,relatime,hugetlb)
cgroup on /sys/fs/cgroup/rdma type cgroup (rw,nosuid,nodev,noexec,relatime,rdma)
cgroup on /sys/fs/cgroup/perf_event type cgroup (rw,nosuid,nodev,noexec,relatime,perf_event)
cgroup on /sys/fs/cgroup/cpuset type cgroup (rw,nosuid,nodev,noexec,relatime,cpuset)
cgroup on /sys/fs/cgroup/memory type cgroup (rw,nosuid,nodev,noexec,relatime,memory)
systemd-1 on /proc/sys/fs/binfmt_misc type autofs (rw,relatime,fd=28,pgrp=1,timeout=0,minproto=5,maxproto=5,direct,pipe_ino=19700)
hugetlbfs on /dev/hugepages type hugetlbfs (rw,relatime,pagesize=2M)
mqueue on /dev/mqueue type mqueue (rw,relatime)
debugfs on /sys/kernel/debug type debugfs (rw,relatime)
configfs on /sys/kernel/config type configfs (rw,relatime)
fusectl on /sys/fs/fuse/connections type fusectl (rw,relatime)
binfmt_misc on /proc/sys/fs/binfmt_misc type binfmt_misc (rw,relatime)
vmware-vmblock on /run/vmblock-fuse type fuse.vmware-vmblock (rw,nosuid,nodev,relatime,user_id=0,group_id=0,default_permissions,allow_other)
tmpfs on /run/user/1001 type tmpfs (rw,nosuid,nodev,relatime,size=98508k,mode=700,uid=1001,gid=1001)
gvfsd-fuse on /run/user/1001/gvfs type fuse.gvfsd-fuse (rw,nosuid,nodev,relatime,user_id=1001,group_id=1001)
/dev/sr0 on /media/user1/Fedora-WS-Live-28-1-1 type iso9660 (ro,nosuid,nodev,relatime,nojoliet,check=s,map=n,blocksize=2048,uid=1001,gid=1001,dmode=500,fmode=400,uhelper=udisks2)
/dev/sdb1 on /mnt/mydisk type ext4 (rw,relatime,data=ordered)
```

#### mount options

 The `mount` command has several options that override the default behavior. For example, we can mount a filesystem read-only by specifying `-o ro`. If the filesystem is already mounted, add `remount:`

```
root@ubuntu16-1:/mnt# mount -o remount,ro /dev/sdb1 /mnt/mydisk/
root@ubuntu16-1:/mnt# cd /mnt/mydisk/
root@ubuntu16-1:/mnt/mydisk# ls
file1.txt  lost+found
root@ubuntu16-1:/mnt/mydisk# touch file2
touch: cannot touch 'file2': Read-only file system
```

> `notes:`

> * Use commas to separate multiple options, such as `remount` and `ro`.
> * When remounting an already mounted filesystem, it suffices to specify either the mount point or the device name. It is not necessary to specify both.
> * You cannot mount a read-only filesystem as read-write. Media that cannot be modified, such as CD-ROM discs, will automatically be mounted read-only.
> * To remount a writable device read-write, specify`-o remount,rw`

| mount command examples                 | description                                                                |
| -------------------------------------- | -------------------------------------------------------------------------- |
| mount -l                               | Add the ext2, ext3 and XFS labels in the mount output.                     |
| mount -r /dev/sdb1 /mydisk             | Mount the file system read-only. Same as -o ro                             |
| mount -r /dev/sdb1 /mydisk             | Mount the file system read/write. This is the default. It is same as -o rw |
| mount -L mydata /mydisk/               | Mount the partition that has the specified label                           |
| mount -U uuid /mydisk                  | Mount the partition that has the specified uuid                            |
| mount -t iso9660 -o ro /dev/cdrom /mnt | Mount a CD-ROM(just in case system didn't that)                            |
| mount -o loop /mydisk.iso /mnt         | Mount an iso image                                                         |

 ` mount -B /mydisk /mysecondplace` :After mounting to a directory the mount point can be changed. We will provide the current mounted point and new mount point in a row with `-B` parameter. This actually does not removes old mount only adds the new directory as the mount.

> All mounts that we create are not permanent and get vanished as system restarted. In order to make a them permanent we need to edit /etc/fstab file. 

### umount

Once a file system is mounted, we can use the umount command (without an “n”) to unmount the file system. We can unmount the file system by using umount with the device or the mount point.

```
umount device
OR
umount mount-point
```

>  Note that a file system cannot be unmounted when it is busy - for example, when there are open files on it, or when some process has its working directory there, or when a swap file on it is in use.

 lets unmount /dev/sdb1 as an example:

```
umount /dev/sdb1
```

### /etc/fstab

In previous sections about Boot Managers we  learned  in both GRUB and LILO  root= parameter  tell the boot loader what filesystem should be mounted as root. For GRUB2, this is the`setroot` statement. Once the root filesystem is mounted, the initialization process runs `mount` with the `-a` option to automatically mount a set of filesystems. The set is specified in the file /etc/fstab.

Fstab is  operating system’s file system table:

```
root@ubuntu16-1:~# cat /etc/fstab 

# /etc/fstab: static file system information.
#
# Use 'blkid' to print the universally unique identifier for a
# device; this may be used with UUID= as a more robust way to name devices
# that works even if disks are added and removed. See fstab(5).
#
# <file system> <mount point>   <type>  <options>       <dump>  <pass>
# / was on /dev/sda1 during installation
UUID=142a64e5-96f3-4789-9c91-1dc1570057b7 /               ext4    errors=remount-ro 0       1
# swap was on /dev/sda5 during installation
UUID=b4801c8b-ca75-4548-8697-182d1b6d895c none            swap    sw              0       0
/dev/fd0        /media/floppy0  auto    rw,user,noauto,exec,utf8 0       0
```

*  **file system :**This may be a device name such as /dev/sda1, or a label (LABEL=) or UUID (UUID=). 

> > We can either mount using device name, labels or uuid. Which one is better ?Every body can set any labels the he/she wants, Device name might be changed for example if you attach two usb devices, so the best way is using uuid for mounting(use `blkid `command).

*  **mount point : **mount point, but** **For swap space, this should be the value ‘none’ or ‘swap’.
*  **type : **Specifies the type of filesystem. it can be (**ext2,3,4, reiserfs,swap, vfat and ntfs,ISO9660,auto**)
* **option** : Specifies the mount options. Specify defaults if you want default mount options.

{% hint style="info" %}
* sync/async - All I/O to the file system should be done (a)synchronously.
* auto - The filesystem can be mounted automatically (at bootup, or when mount is passed the -a option). This is really unnecessary as this is the default action of mount -a anyway.
* noauto - The filesystem will NOT be automatically mounted at startup, or when mount passed -a. You must explicitly mount the filesystem.
* dev/nodev - Interpret/Do not interpret character or block special devices on the file system.
* exec / noexec - Permit/Prevent the execution of binaries from the filesystem.
* suid/nosuid - Permit/Block the operation of suid, and sgid bits.
* ro - Mount read-only.
* rw - Mount read-write.
* user - Permit any user to mount the filesystem. This automatically implies noexec, nosuid,nodev unless overridden.
* nouser - Only permit root to mount the filesystem. This is also a default setting.
* defaults - Use default settings. Equivalent to rw, suid, dev, exec, auto, nouser, async.
* \_netdev - this is a network device, mount it after bringing up the network. Only valid with fstype nfs.
{% endhint %}

> note1: User-mounted filesystems default to noexec unless exec is specified afteruser.
>
> note2: noatime will disable recording of access times. Not using access times may improve performance.

* **dump: **Specifies whether the dump command should consider this ext2 or ext3 filesystem for backups. A value of 0 tells dump to ignore this filesystem.
* **pass: **Non-zero values of pass specify the order of checking filesystems at boot time,( based on check interval and mount count, try `tune2fs -l` ).

Lets add /dev/sdb1 to the /etc/fstab file using UUID  and have it even after reboot:

```
root@ubuntu16-1:~# blkid
/dev/sdb1: LABEL="mydata" UUID="03c28ca3-daa3-49c2-9bc4-b083c3b0957b" TYPE="ext4" PARTUUID="4d71bc84-01"
/dev/sda1: LABEL="myroot" UUID="142a64e5-96f3-4789-9c91-1dc1570057b7" TYPE="ext4" PARTUUID="101c66bb-01"
/dev/sda5: UUID="b4801c8b-ca75-4548-8697-182d1b6d895c" TYPE="swap" PARTUUID="101c66bb-05"
/dev/sr0: UUID="2018-04-25-06-43-09-00" LABEL="Fedora-WS-Live-28-1-1" TYPE="iso9660" PTUUID="3a663a44" PTTYPE="dos"

root@ubuntu16-1:~# vim /etc/fstab 
```

```
# /etc/fstab: static file system information.
#
# Use 'blkid' to print the universally unique identifier for a
# device; this may be used with UUID= as a more robust way to name devices
# that works even if disks are added and removed. See fstab(5).
#
# <file system> <mount point>   <type>  <options>       <dump>  <pass>
# / was on /dev/sda1 during installation
UUID=142a64e5-96f3-4789-9c91-1dc1570057b7 /               ext4    errors=remount-ro 0       1
# swap was on /dev/sda5 during installation
UUID=b4801c8b-ca75-4548-8697-182d1b6d895c none            swap    sw              0       0
/dev/fd0        /media/floppy0  auto    rw,user,noauto,exec,utf8 0       0
### added by root! 
UUID=03c28ca3-daa3-49c2-9bc4-b083c3b0957b /mnt/mydisk  auto default   0 0

```

if any thing goes wrong in /etc/fstab , it can cause system failure and system can't boots up! So try `mount -a` in order to try mounting all fstab items before rebooting the system and check for errors.

```
root@ubuntu16-1:~# mount -a
mount: wrong fs type, bad option, bad superblock on /dev/sdb1,
       missing codepage or helper program, or other error

       In some cases useful info is found in syslog - try
       dmesg | tail or so.
```

Ops! that is defaults not default, so try to edit this line again in fstab:

```
### added by root! 
UUID=03c28ca3-daa3-49c2-9bc4-b083c3b0957b /mnt/mydisk  auto defaults   0 0
```

and we are done:

```
root@ubuntu16-1:~# mount -a
root@ubuntu16-1:~# cd /mnt/mydisk/
root@ubuntu16-1:/mnt/mydisk# ls
file1.txt  file2.txt  lost+found
```

users can only mount and unmount things they are allowed to in /etc/fstab!

.

.

.

[https://developer.ibm.com/tutorials/l-lpic1-104-3/](https://developer.ibm.com/tutorials/l-lpic1-104-3/)

[https://www.geeksforgeeks.org/mount-command-in-linux-with-examples/](https://www.geeksforgeeks.org/mount-command-in-linux-with-examples/)

[https://www.sanfoundry.com/mount-command-usage-example/](https://www.sanfoundry.com/mount-command-usage-example/)

[https://www.poftut.com/linux-mount-command-tutorial-examples/](https://www.poftut.com/linux-mount-command-tutorial-examples/)

[https://www.thegeekdiary.com/how-to-mount-and-umount-a-file-system-in-linux/](https://www.thegeekdiary.com/how-to-mount-and-umount-a-file-system-in-linux/)

[https://help.ubuntu.com/community/Fstab](https://help.ubuntu.com/community/Fstab)

[https://linoxide.com/file-system/understanding-each-entry-of-linux-fstab-etcfstab-file/](https://linoxide.com/file-system/understanding-each-entry-of-linux-fstab-etcfstab-file/)

.
