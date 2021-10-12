# 104.4. Manage disk quotas

**Weight: **1

**Description:** Candidates should be able to manage disk quotas for users.

**Key Knowledge Areas:**

* Set up a disk quota for a filesystem
* Edit, check and generate user quota reports

**Terms and Utilities:**

* quota
* edquota
* repquota
* quotaon



Quotas allow us to control disk usage by user or by group. Quotas prevent individual users and groups from using a larger portion of a filesystem than they are permitted, or from filling it up altogether. Here for LPIC2 exam we use quota version 2 with kernel  2.4 and above.

\[In this Lesson we will enable and set quota on /dev/sdb1 with ext4 file system.]

#### Checking for the quota package <a href="checking-for-the-quota-package" id="checking-for-the-quota-package"></a>

 Quota package is not installed since quotas are not part of the usual default desktop install. Use `dpkg` or `rpm` to check that you have the package installed.

```
root@ubuntu16-1:~/mydisk# dpkg -l | grep quota
root@ubuntu16-1:~/mydisk# apt install quota
```

#### Adding quota support to /etc/fstab <a href="adding-quota-support-to-etc-fstab" id="adding-quota-support-to-etc-fstab"></a>

First step to enable quotas is to add the appropriate options to the filesystem definitions in /etc/fstab, according to whether we want to implement user quotas, group quotas.

| option   | meaning             |
| -------- | ------------------- |
| usrquota | Enable user quotas  |
| grpquota | Enable group quotas |

So for enabling quotas on sdb1 we need to edit the line we have previously added to /etc/fstab (ubuntu 16.04):

```
UUID=03c28ca3-daa3-49c2-9bc4-b083c3b0957b /mnt/mydisk	auto defaults,usrquota,grpquota 	0 0
```

After editing  /etc/fstab and add quotas, we need to remount the filesystems.

> mount -o remount /dev/sdb1

user quota information is stored in the aquota.user file in the root of the filesystem, and group quota is similarly stored in aquota.group.

after we remount the filesystem, we must create the quota files and enable quota checking.

```
root@ubuntu16-1:~# cd /root/mydisk/
root@ubuntu16-1:/mnt/mydisk# ls
file1.txt  file2.txt  lost+found
```

#### quotacheck

 The `quotacheck` command checks the quotas on all filesystems and creates the required aquota.user and aquota.group files if they do not exist. It can also repair damaged quota files. 

| quotacheck switch | meaning                                                                   |
| ----------------- | ------------------------------------------------------------------------- |
| -a                | Check all mounted filesystems in /etc/mtab (except NFS filesystems)       |
| -c                | Ignore existing quota files. Run a new scan and write the results to disk |
| -u                | Check user quotas (this is the default)                                   |
| -g                | Check group quotas                                                        |
| -v                | Verbose output                                                            |

```
root@ubuntu16-1:/mnt/mydisk# quotacheck -acugv
quotacheck: Your kernel probably supports journaled quota but you are not using it. Consider switching to journaled quota to avoid running quotacheck after an unclean shutdown.
quotacheck: Scanning /dev/sdb1 [/root/mydisk] done
quotacheck: Cannot stat old user quota file /root/mydisk/aquota.user: No such file or directory. Usage will not be subtracted.
quotacheck: Cannot stat old group quota file /root/mydisk/aquota.group: No such file or directory. Usage will not be subtracted.
quotacheck: Cannot stat old user quota file /root/mydisk/aquota.user: No such file or directory. Usage will not be subtracted.
quotacheck: Cannot stat old group quota file /root/mydisk/aquota.group: No such file or directory. Usage will not be subtracted.
quotacheck: Checked 2 directories and 2 files
quotacheck: Old file not found.
quotacheck: Old file not found.

root@ubuntu16-1:/mnt/mydisk# ls
aquota.group  aquota.user  file1.txt  file2.txt  lost+found
```

aquota.user and aquota.group have been made.

{% hint style="danger" %}
aquota.user and aquota.group are both binary files and should not be edited by hand.

```
root@ubuntu16-1:/mnt/mydisk# cat aquota.group 
'���:	�:	proot@ubuntu16-1:~/mydisk# ^C
```
{% endhint %}

#### Turning quota  on /off <a href="start-or-stop-quota-checking" id="start-or-stop-quota-checking"></a>

### quotaon

The quotaon command enables disk quotas for one or more file systems specified by the FileSystem parameter. The specified file system must be defined with quotas in the /etc/filesystems file, and must be mounted.

 The quotaon command looks for the quota.user and quota.group files in the root directory of the associated file system, and will return an error if not found.

```
oot@ubuntu16-1:/mnt/mydisk# ls
aquota.group  aquota.user  file1.txt  file2.txt  lost+found

root@ubuntu16-1:/mnt/mydisk# quotaon /dev/sdb1
#OR
root@ubuntu16-1:/mnt/mydisk# quotaon /root/mydisk
```

 The common options `-a`, `-g`, `-u`, and `-v` have the same meaning as for the `quotacheck command`. Similarly, if you do not specify the `-a` option, you must specify a filesystem. Use the `-p` option if you just want to display whether quotas are on or off.

> **quotaoff **announces to the system that the specified filesystems should have any disk quotas turned off.

#### Setting quota limits

### edquota

 Quotas are controlled either through binary files in the root of the filesystem or through filesystem metadata. To set a quota for a particular user, use the `edquota` command. This command extracts the quota information for the user from the various filesystems with quotas enabled, creates a temporary file, and opens an editor for us to adjust the quotas.

*  use the `-u` option (default) with one or more user names. 
* To edit group quotas, use the `-g` with one or more group names.

```
root@ubuntu16-1:/mnt/mydisk# edquota -u user1
```

```
Disk quotas for user user1 (uid 1001):
  Filesystem                   blocks       soft       hard     inodes     soft     hard
  /dev/sdb1                       0         0           0          0        0        3
```

 `edquota` displays my current usage of both 1K blocks and inodes on each of the filesystems that have quota turned on. 

You can think of block limits as an approximate limit on the amount of data that a user may store, and inode limits as a limit on the number of files and directories.

There are also soft and hard limits for both block and inode usage. In this example, these are 0, meaning no quota limit is enforced.

{% hint style="info" %}
**soft limit vs hard limit**

The soft limit is the value at which a user will receive email warnings about being over quota. The hard limit is the value that a user may not exceed.

#### The grace period <a href="the-grace-period" id="the-grace-period"></a>

 Users or groups may exceed their soft limit for a _grace period_, which defaults to **seven days**. After the grace period, the soft limit is enforced as a hard limit. Once the hard limit is reached, some files must be deleted before new files can be created. 

Set grace periods using edquota -t`edquota-t`. Again, you will be placed in an editor:

```
Time units may be: days, hours, minutes, or seconds
  Filesystem             Block grace period     Inode grace period
  /dev/sdb1                     7days                  7days
```
{% endhint %}

now lets set some hard limits on  inodes on /dev/sdb1 for user1: 

```
root@ubuntu16-1:/mnt/mydisk# edquota -u user1
```

```
Disk quotas for user user1 (uid 1001):
  Filesystem                   blocks       soft       hard     inodes     soft     hard
  /dev/sdb1                         0          0          0          0        0        3
```

and lets check it :

```
user1@ubuntu16-1:/mnt/mydisk$ ls
aquota.group  aquota.user  file1.txt  file2.txt  lost+found
user1@ubuntu16-1:/mnt/mydisk$ touch user1file1
user1@ubuntu16-1:/mnt/mydisk$ touch user1file2
user1@ubuntu16-1:/mnt/mydisk$ touch user1file3
user1@ubuntu16-1:/mnt/mydisk$ touch user1file4
touch: cannot touch 'user1file4': Disk quota exceeded
```

For copying one user quota limits to the other user, use -p switch:

`edquota ‑p user1 user2 user2`

#### Quota reports

### quota

 The `quota` command with no options displays the quotas for the a user on any filesystems (quota shoud have been set for that user and he or she must have some files on it): 

```
root@ubuntu16-1:/mnt/mydisk# quota -u user1
Disk quotas for user user1 (uid 1001): 
     Filesystem  blocks   quota   limit   grace   files   quota   limit   grace
      /dev/sdb1       0       0       0               3*      0       3        
```

> The `-v` option displays the information for all filesystems that have quota enabled. 

 Checking user quotas one user at a time is not very useful, so you will want to use the `repquota` command to generate quota reports.

### repquota 

repquota stands for report for quota. This will display filesystem usage and quota information on your system.

```
root@ubuntu16-1:/mnt/mydisk# repquota /dev/sdb1
*** Report for user quotas on device /dev/sdb1
Block grace time: 7days; Inode grace time: 7days
                        Block limits                File limits
User            used    soft    hard  grace    used  soft  hard  grace
----------------------------------------------------------------------
root      --      28       0       0              4     0     6       
user1     --       0       0       0              3     0     3      
```

>  `-a` option produces a report for all mounted filesystems that have quota enabled. The `-v` option produces more verbose

That's all!

.

.

.

{% hint style="info" %}
### Warning users <a href="warning-users" id="warning-users"></a>

 The `warnquota` command is used to send email warnings to users who are over quota. When a group is over quota, the email is sent to the user specified in /etc/quotagrpadmins for the group. The format of the email is controlled by the file /etc/warnquota.conf. The file /etc/quotatab is used to map names such as /dev/sdc6 to more user-friendly descriptions such as “Shared EXT3 filesystem.” Normally `warnquota` is run periodically as a `cron` job( will be discussed later).
{% endhint %}

.

[https://developer.ibm.com/tutorials/l-lpic1-104-4/](https://developer.ibm.com/tutorials/l-lpic1-104-4/)

[https://www.ibm.com/support/knowledgecenter/ssw_aix\_71/q_commands/quotaon.html](https://www.ibm.com/support/knowledgecenter/ssw_aix\_71/q_commands/quotaon.html)

[https://linux.101hacks.com/unix/repquota/](https://linux.101hacks.com/unix/repquota/)

.
