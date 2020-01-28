# 104.5. Manage file permissions and ownership

**Weight:** 3

**Description:** Candidates should be able to control file access through the proper use of permissions and ownerships.

**Key Knowledge Areas:**

* Manage access permissions on regular and special files as well as directories
* Use access modes such as suid, sgid and the sticky bit to maintain security
* Know how to change the file creation mask
* Use the group field to grant file access to group members

**Terms and Utilities:**

* chmod
* umask
* chown
* chgrp

####  <a id="users-groups-and-file-ownership"></a>

#### Users, groups and file ownership <a id="users-groups-and-file-ownership"></a>

By now, you know that Linux is a multiuser system and that each user belongs to one primary group and possibly additional groups. It is also possible to log in as one user and become another user using the `su`  commands. Ownership of files in Linux and access authority are closely related to user ids and groups.

### User and groups <a id="user-and-groups"></a>

To start, let’s review some basic user and group information via some commands

* whoami : It displays the username of the current user 

```text
user1@ubuntu16-1:~/sandbox$ whoami
user1
user1@ubuntu16-1:~/sandbox$ su -
Password: 
root@ubuntu16-1:~# whoami
root
```

* groups: We can find out what groups you are in by using the `groups` command.

```text
root@ubuntu16-1:~# groups
root
root@ubuntu16-1:~# exit
logout
user1@ubuntu16-1:~/sandbox$ groups
user1
```

* id :  We can find out both user and group information using the `id` command.

```text
user1@ubuntu16-1:~/sandbox$ id
uid=1001(user1) gid=1001(user1) groups=1001(user1)
user1@ubuntu16-1:~/sandbox$ su -
Password: 
root@ubuntu16-1:~# id
uid=0(root) gid=0(root) groups=0(root)
```

> It can show numeric ID’s \(UID or group ID\) of the current user or any other user in the server.

{% hint style="info" %}
users and groups information are stored in /etc/passwd and /etc/group along other information.

```text
root@ubuntu16-1:~# cat /etc/passwd | grep user1
user1:x:1001:1001::/home/user1:
root@ubuntu16-1:~# cat /etc/group | grep user1
user1:x:1001:
```
{% endhint %}

#### File ownership and permissions

Every file on a Linux system has one owner and one group associated with it.

 Use the ls -l`ls-l` command to display the owner and group.

```text
user1@ubuntu16-1:~/sandbox$ ls -l
total 4
drwxrwxr-x 2 user1 user1 4096 Jan 27 21:49 dir1
-rw-rw-r-- 1 user1 user1    0 Jan 27 21:49 file1
```

> As you can see, the file1 belongs to user1 and a group called user1.

The first character of a long listing describes the type of object. "-" for a regular file, "d" for a directory, "l" for a symbolic link\(we will see them\).

![](.gitbook/assets/permis-filepermis.jpg)

Permissions are specified separately for the file’s owner, members of the file’s group, and everyone else. 

The Linux permission model has three types of permission for each filesystem object.

The permissions are read \(r\), write \(w\), and execute \(x\). Write permission includes the ability to alter or delete an object.   A `-` indicates that the corresponding permission is not granted. example:

```text
root@ubuntu16-1:~# ls -l /sbin/fsck
-rwxr-xr-x 1 root root 44184 May 16  2018 /sbin/fsck
```

As you can see fsck can be read, written and executed by its owner \(root\) and all  root group members, but others can just read and execute that\(probably with limited results \)

#### Directories ownership and permissions <a id="directories"></a>

![](.gitbook/assets/permis-dirpermis.jpg)

Directories use the same permissions flags as regular files, but they are interpreted differently.

* Read permission for a directory allows a user with that permission to list the contents of the directory.
* Write permission means a user with that permission can create or delete files in the directory.
* Execute permission allows the user to enter the directory and access any subdirectories.

Without execute permission on a directory, the filesystem objects inside the directory are not accessible. Without read permission on a directory, the filesystem objects inside the directory are not viewable in a directory listing, but these objects can still be accessed as long as you know the full path to the object on disk. example:

```text
root@ubuntu16-1:~# ls -l /home/
total 8
drwxr-xr-x 22 payam payam 4096 Oct 27  2018 payam
drwxr-xr-x 19 user1 user1 4096 Jan 27 21:49 user1
```

The first charcter indicates that this a directory. The owner \(user1\) has read,write, execute access but other members of user1 group and others have just read and execute access on this directory, \(as we mentioned, execute lets them to see files inside it \)

#### Changing permissions

### chmod

The command you use to change the  permissions on files is called chmod , which stands for “change mode". There  are to ways to tell this command what you want to do:

* using short codes
* using ocatl codes

**1- using short codes:**  That is easier way. 

![](.gitbook/assets/permis-chmodshortcodes.jpg)

```text
chmod [reference][operator][mode] file... 
```

reference can be

*  u as user  \(file's owner\)
* g as group \(users who are members of the file's grou\)
* o as others \(users who are not the file's owner / members of the file's group\)
* a as all \(All three of the above, same as ugo\)

Operator can be

* +  Adds the specified modes to the specified classes
*  - Removes the specified modes from the specified classes
* = The modes specified are to be made the exact modes for the specified classes

obviously modes might be

* r  :Permission to read the file
* w :Permission to write \(or delete\) the file.
* x : Permission to execute the file, or, in the case of a directory, search it.

```text
user1@ubuntu16-1:~/sandbox$ ls -l | grep file1
-rw-rw-r-- 1 user1 user1    0 Jan 27 21:49 file1

user1@ubuntu16-1:~/sandbox$ chmod u+x file1

user1@ubuntu16-1:~/sandbox$ ls -l | grep file1
-rwxrw-r-- 1 user1 user1    0 Jan 27 21:49 file1

user1@ubuntu16-1:~/sandbox$ chmod o-r file1

user1@ubuntu16-1:~/sandbox$ ls -l | grep file1
-rwxrw---- 1 user1 user1    0 Jan 27 21:49 file1
```

> If we want to set different permissions for user, group, or other, we can separate different expressions by commas —for example, `ug=rwx,o=rx`

```text
user1@ubuntu16-1:~/sandbox$ ls -l | grep file1
-rwxrw---- 1 user1 user1    0 Jan 27 21:49 file1

user1@ubuntu16-1:~/sandbox$ chmod u-x,g+x,o+r file1

user1@ubuntu16-1:~/sandbox$ ls -l | grep file1
-rw-rwxr-- 1 user1 user1    0 Jan 27 21:49 file1
```

> using a as ugo with = operator to set exact mode easier

```text
user1@ubuntu16-1:~/sandbox$ ls -l | grep file1
-rw-rwxr-- 1 user1 user1    0 Jan 27 21:49 file1
user1@ubuntu16-1:~/sandbox$ chmod a=rw file1
user1@ubuntu16-1:~/sandbox$ ls -l | grep file1
-rw-rw-rw- 1 user1 user1    0 Jan 27 21:49 file1
```

**2- using ocatl codes :** So far we have used symbols \(ugoa and rxw\) to specify permissions. we can also set permissions using octal numbers instead of symbols.

![](.gitbook/assets/permis-chmodoctalcodes.jpg)



To change permissions  recursively on directories and files use -R option:

.

.

.

[https://developer.ibm.com/tutorials/l-lpic1-104-5/](https://developer.ibm.com/tutorials/l-lpic1-104-5/)

[https://jadi.gitbooks.io/lpic1/content/1045\_manage\_file\_permissions\_and\_ownership.html](https://jadi.gitbooks.io/lpic1/content/1045_manage_file_permissions_and_ownership.html)

.

