# 107.1. Manage user and group accounts and related system files

## **107.1 Manage user and group accounts and related system files**

**Weight:** 5

**Description: **Candidates should be able to add, remove, suspend and change user accounts.

**Key Knowledge Areas:**

* Add, modify and remove users and groups
* Manage user/group info in password/group databases
* Create and manage special purpose and limited accounts

**Terms and Utilities:**

* /etc/passwd
* /etc/shadow
* /etc/group
* /etc/skel/
* chage
* getent
* groupadd
* groupdel
* groupmod
* passwd
* useradd
* userdel
* usermod

## Changing password

### passwd

The passwd command changes passwords for user accounts. A normal user can only change the password for their own account, but the superuser can change the password for any account.

```
user1@ubuntu16-1:~$ passwd 
Changing password for user1.
(current) UNIX password: 
Enter new UNIX password: 
Retype new UNIX password: 
passwd: password updated successfully
```

1. Before a normal user can change their own password, they must first enter their current password for verification. (The superuser can bypass this step when changing another user's password.)
2. After the current password has been verified, **passwd** checks to see if the user is allowed to change their password at this time or not. Then user is then prompted twice.
3. Next, the password is tested for complexity.passwords should consist of at least 6 characters.

The root user can change any users password to anything (weak passwords) without providing their current password:

```
root@ubuntu16-1:~# passwd user1
Enter new UNIX password: 
Retype new UNIX password: 
passwd: password updated successfully
```

>  Groups can also have passwords, which you set with the `gpasswd` command, but it is not used at all!

## Users and Groups

We have  learned  that Linux is a multiuser system.Recall that we can log in as one user and become another user by using the su or sudo commands. 

Linux also has the concept of groups . 

* each user belongs to one primary group and possibly to additional groups. 
* Each file belongs to one user and one group

We learn how to create, delete, and manage users and groups.

## Managing users 

### useradd

 We add a user to a Linux system with the `useradd` command.

```
 useradd <options> <username_or_login>
```

| switch | description                                  |
| ------ | -------------------------------------------- |
| -d     | home directory of the new account            |
| -m     | create the user's home directory             |
| -s     | login shell of the new account               |
| -G     | add to Additional Groups                     |
| -c     | comment, most of the time user's actual name |

In most distributions  useradd creates home directory for the new user but we can make sure using -m switch. example(ubunru 16):

```
root@ubuntu16-1:~# useradd -m -d /home/user3 -c "Dear user3" -s /bin/bash user3
```

### /etc/skel

{% hint style="info" %}
#### The home directory skeleton

When you create a new user and a new home directory is created, the directory is populated with several files and subdirectories that, by default, are copied from /etc/skel.

```
root@ubuntu16-1:~# ls -a /etc/skel/
.  ..  .bash_logout  .bashrc  examples.desktop  .profile
```
{% endhint %}

### usermod <a href="the-home-directory-skeleton" id="the-home-directory-skeleton"></a>

 We can use the `usermod` command to modify a user account. we can use most of the options that you use with `useradd`, except that you cannot create or populate a new home directory for the user.

```
usermod <options> <username_or_login>
```

| switch | description                                                                                 |
| ------ | ------------------------------------------------------------------------------------------- |
| -L     | lock the user account                                                                       |
| -U     | unlock the user account                                                                     |
| -g     | force use GROUP as new primary group                                                        |
| -G     | new list of Additional GROUPS ( user will be  removed from all previous Additional groups ) |
| -aG    | append the user to the Additional  GROUPS(without removing him/her from other groups)       |

```
root@ubuntu16-1:~# id user3
uid=1003(user3) gid=1003(user3) groups=1003(user3)

root@ubuntu16-1:~# usermod -g user1 user3
root@ubuntu16-1:~# id user3
uid=1003(user3) gid=1001(user1) groups=1001(user1),1003(user3)

root@ubuntu16-1:~# usermod -G user2 user3
root@ubuntu16-1:~# id user3
uid=1003(user3) gid=1001(user1) groups=1001(user1),1002(user2)

root@ubuntu16-1:~# usermod -aG payam user3
root@ubuntu16-1:~# id user3
uid=1003(user3) gid=1001(user1) groups=1001(user1),1000(payam),1002(user2)

###lets turn back to the deafult settings
root@ubuntu16-1:~# usermod -g user3 -G user3 user3
root@ubuntu16-1:~# id user3
uid=1003(user3) gid=1003(user3) groups=1003(user3)
```

### userdel

 We can delete a user with the `userdel` command.

```
userdel <options> <username_or_login>
```

userdel by default does not remove user's home directory.

| switch | description                          |
| ------ | ------------------------------------ |
| -f     | force removal of files               |
| -r     | remove home directory and mail spool |

```
root@ubuntu16-1:~# userdel -f -r user3
userdel: user3 mail spool (/var/mail/user3) not found
```

### Managing Groups

 Similarly, we can add or delete groups with the `groupadd` and `groupdel` commands.

### groupadd 

```
groupadd [options] group
```

| switch | description                                                                             |
| ------ | --------------------------------------------------------------------------------------- |
| -g     | use GID for the new group                                                               |
| -f     | exit successfully if the group already exists, and cancel -g if the GID is already used |
| -p     | use this encrypted password for the new group                                           |

```
root@ubuntu16-1:~# groupadd -g 666  group1
root@ubuntu16-1:~# groupadd -g 666  group1
groupadd: group 'group1' already exists
root@ubuntu16-1:~# echo $?
9
root@ubuntu16-1:~# groupadd -f -g 666  group1
root@ubuntu16-1:~# echo $?
0

```

### groupmod

 When you need to modify group information, use the `groupmod` command.

```
groupmod [options] GROUP
```

| switch | description           |
| ------ | --------------------- |
| -n     | change the group name |
| -g     | change the group ID   |

```
root@ubuntu16-1:~# groupmod -n newgroup1 group1
root@ubuntu16-1:~# groupmod -g 888 newgroup1 
```

### groupdel

  In fact, the `groupdel` command to delete a group requires only the group name; it has no options. You cannot delete any group that is the primary group of a user.

```
root@ubuntu16-1:~# groupdel newgroup1
```

> Note: If root deletes a group with members, people wont be deleted! They will just wont be the members of that group anymore.



When we run ‘**useradd**‘ command in Linux terminal, it performs following major things:

1. It edits /etc/passwd, /etc/shadow, /etc/group and /etc/gshadow files for the newly created User account.
2. Creates and populate a home directory for the new user.
3. Sets permissions and ownerships to home directory.

What are those files?

### /etc/passwd

 /etc/passwd is the _password_ file containing basic information about users.

```
root@ubuntu16-1:~# tail /etc/passwd
pulse:x:117:124:PulseAudio daemon,,,:/var/run/pulse:/bin/false
rtkit:x:118:126:RealtimeKit,,,:/proc:/bin/false
saned:x:119:127::/var/lib/saned:/bin/false
usbmux:x:120:46:usbmux daemon,,,:/var/lib/usbmux:/bin/false
payam:x:1000:1000:ubuntu16.04.3-1,,,:/home/payam:/bin/bash
user1:x:1001:1001::/home/user1:
sshd:x:121:65534::/var/run/sshd:/usr/sbin/nologin
mysql:x:122:129:MySQL Server,,,:/nonexistent:/bin/false
user2:x:1002:1002::/home/user2:
postfix:x:123:130::/var/spool/postfix:/bin/false
```

it has one line for each user in the system. the format of it is :

![](.gitbook/assets/usergroup-passwd.jpg)

1. **Username**:  should be between 1 and 32 characters 
2. **Password **_(will be discussed)_
3. **User ID (UID)**: Each user must be assigned a user ID (UID). UID 0 (zero) is reserved for root and UIDs 1-99 are reserved for other predefined accounts. Further UID 100-999 are reserved by system for administrative and system accounts/groups. 
4. **Group ID (GID)**: The primary group ID (stored in /etc/group file) 
5. **The comment field**. It allow you to add extra information about the users such as user’s full name, phone number etc. This field use by finger command. 
6. **Home directory**
7. **Command/shell**: The absolute path of a command or shell (/bin/bash). Typically, this is a shell. It does not have to be a shell.

> There are some users with /sbin/nologin shell, They are actually system accounts that run a service and no one can interactively login using them. Some times it has been set to /bin/false. 

Every user should have read access to /etc/passwd  :

```
root@ubuntu16-1:~# ls -l /etc/passwd
-rw-r--r-- 1 root root 2469 Feb 12 02:53 /etc/passwd
```

In old days there was a place that  all users information even the user's password, and it is not so hard thick about security issue that it caused. To solve the problem /etc/shadow was invented.  An x character indicates that encrypted password is stored in /etc/shadow file

### /etc/shadow

The /etc/shadow file contains encrypted passwords, along with password- and account-expiration information.

```
root@ubuntu16-1:~# ls -l /etc/shadow
-rw-r----- 1 root shadow 1609 Feb 12 02:53 /etc/shadow
```

Lets see what's inside that:

```
root@ubuntu16-1:~# tail /etc/shadow
pulse:*:17379:0:99999:7:::
rtkit:*:17379:0:99999:7:::
saned:*:17379:0:99999:7:::
usbmux:*:17379:0:99999:7:::
payam:$1$jYgAdos4$Je8la0839ZRVgazhnBpDv1:17496:0:99999:7:::
user1:$6$c9PN.175$.t.CG0E0Gtr/trq4pqquSe1BemMjB6Zc3E0ExUOVufuTkPNe3BSRv3DyUuXFHPiAbEujzuSMCeMsCbpg8cV2j.:17749:0:99999:7:::
sshd:*:17749:0:99999:7:::
mysql:!:17867:0:99999:7:::
user2:$6$kN2DNYrP$XmM/3ONRnrTCuTTBxCwVBlVW9E4tVRc02JbRHPhwj128Q6aUIcUq4gxw2r74gopOs2J0HqNxuiBiqgAlkmuwV1:18290:0:99999:7:::
postfix:*:18300:0:99999:7:::
```

> Note: !! means user can not log in with any passwords. Most of service accounts are like this.

Passwords can be encrypted with one of several encryption algorithms. Older systems used DES or MD5, but modern systems typically use Blowfish, SHA-256, or SHA-512, or possibly MD5. Regardless of encryption algorithm, passwords are _salted_ so that two otherwise identical passwords do not generate the same encrypted value.

![](.gitbook/assets/usergroup-shadow.jpg)

1. **Username** : It is your login name. 
2. **Password** : It is your encrypted password. The password should be minimum 8-12 characters long including special characters, digits, lower case alphabetic and more. Usually password format is set to `$id$salt$hashed`, _The $id is the algorithm used On GNU/Linux as follows: $1$ is MD5 $2a$ is Blowfish $2y$ is Blowfish $5$ is SHA-256 $6$ is SHA-512 _
3. **Last password change (lastchanged)** : Days since Jan 1, 1970 that password was last changed 
4. **Minimum **: The minimum number of days required between password changes i.e. the number of days left before the user is allowed to change his/her password
5. **Maximum **: The maximum number of days the password is valid (after that user is forced to change his/her password)
6.  **Warn** : The number of days before password is to expire that user is warned that his/her password must be changed 
7. **Inactive **: The number of days after password expires that account is disabled 
8. **Expire** : days since Jan 1, 1970 that account is disabled i.e. an absolute date specifying when the login may no longer be used.

The last 6 fields provides password aging and account lockout features. You need to use the chage command to setup password aging.

{% hint style="info" %}
**epoch time**

Unix time is a system for describing a point in time. It is the number of seconds that have elapsed since the Unix epoch, that is the time 00:00:00 UTC on 1 January 1970, minus leap seconds.
{% endhint %}

### chage

 **chage** command is used to view and change the user password expiry information. This command is used when the login is to be provided for a user for limited amount of time or when it is necessary to change the login password time to time.

```
chage [options] LOGIN
```

```
Options:
  -d, --lastday LAST_DAY        set date of last password change to LAST_DAY
  -E, --expiredate EXPIRE_DATE  set account expiration date to EXPIRE_DATE
  -h, --help                    display this help message and exit
  -I, --inactive INACTIVE       set password inactive after expiration
                                to INACTIVE
  -l, --list                    show account aging information
  -m, --mindays MIN_DAYS        set minimum number of days before password
                                change to MIN_DAYS
  -M, --maxdays MAX_DAYS        set maximim number of days before password
                                change to MAX_DAYS
  -R, --root CHROOT_DIR         directory to chroot into
  -W, --warndays WARN_DAYS      set expiration warning days to WARN_DAYS

```

chage without any options lets do  editing  all items interactively ,lets try -l option on user1:

```
root@ubuntu16-1:~# chage -l user1
Last password change					: Aug 06, 2018
Password expires					: never
Password inactive					: never
Account expires						: never
Minimum number of days between password change		: 0
Maximum number of days between password change		: 99999
Number of days of warning before password expires	: 7
```

>  `chage -d 0 user-name` will force user to change his password in next login.

> **passwd** can also change or reset the account's validity period — how much time can pass before the password expires and must be changed.

### /etc/group

 /etc/group is the _group_ file containing basic information about groups and which users belong to them. It contains one line for each group in the system.

```
root@ubuntu16-1:~# tail /etc/group
rtkit:x:126:
saned:x:127:
payam:x:1000:
sambashare:x:128:payam
user1:x:1001:
mysql:x:129:
user2:x:1002:
postfix:x:130:
postdrop:x:131:
mysecuregroup:x:1003:
```

![](.gitbook/assets/usergroup-group.jpg)

1. **group_name**: It is the name of group. If you run ls -l command, you will see this name printed in the group field. 
2. **Password**: Generally password is not used, hence it is empty/blank. It can store encrypted password. This is useful to implement privileged groups. 
3. **Group ID (GID)**: Each user must be assigned a group ID. You can see this number in your /etc/passwd file. 
4. **Group List**: It is a list of user names of users who are members of the group. The user names, must be separated by commas.

```
root@ubuntu16-1:~# ls -l /etc/group
-rw-r--r-- 1 root root 1077 Feb 12 03:58 /etc/group
```

Like /etc/passwd file, /etc/group is shadowed for security reasons and must be world readable, but encrypted passwords should not be world readable.

groups password are stored in /etc/gshadow file which is readable only by root.

```
root@ubuntu16-1:~# ls -l /etc/gshadow
-rw-r----- 1 root shadow 902 Feb 12 03:58 /etc/gshadow

root@ubuntu16-1:~# tail /etc/gshadow
rtkit:!::
saned:!::
payam:!::
sambashare:!::payam
user1:!::
mysql:!::
user2:!::
postfix:!::
postdrop:!::
mysecuregroup:Aa12345::
```

> its format is : 
>
> `Group name:Encrypted password:Group administrators: Group members`

> ! :groups can have passwords but it have never been used in any distribution!

### getent

 **getent** is a Linux command that helps the user to get the entries in a number of important text files called databases.

```
getent database [key ...]
```

we use the getent command for processing groups and user information, instead of manually reading /etc/passwd, /etc/groups.

```
root@ubuntu16-1:~# getent passwd payam
payam:x:1000:1000:ubuntu16.04.3-1,,,:/home/payam:/bin/bash

root@ubuntu16-1:~# getent group payam
payam:x:1000:
```

do not forget to use id command.

.

.

.

**Bonus: **Commands and options for changing user accounts

| usermod | passwd          | chage           | Purpose                                                                    |
| ------- | --------------- | --------------- | -------------------------------------------------------------------------- |
| -L      | -l(lowercase L) | N/A             | Lock or suspend the account.                                               |
| -U      | -u              | N/A             | Unlock the account.                                                        |
| N/A     | -d              | N/A             | Disable account by setting it passwordless                                 |
| -e      | -f              | -E              | Set the expiration date for an account.                                    |
| N/A     | -n              | -m              | The minimum password lifetime in days.                                     |
| N/A     | -X              | -M              | The maximum password lifetime in days.                                     |
| N/A     | -W              | -W              | The number of days of warning before a password must be changed.           |
| -f      | -i              | -I(uppercase i) | The number of days after a password expires until the account is disabled. |
| N/A     | -S              | -l(lowercase L) | Output a short message about the current account status.                   |

.

.

.

[https://developer.ibm.com/technologies/linux/tutorials/l-lpic1-map/](https://developer.ibm.com/technologies/linux/tutorials/l-lpic1-map/)

[https://www.computerhope.com/unix/upasswor.htm](https://www.computerhope.com/unix/upasswor.htm)

[https://jadi.gitbooks.io/lpic1/content/1071\_manage_user_and_group_accounts_and_related_system_files.html](https://jadi.gitbooks.io/lpic1/content/1071\_manage_user_and_group_accounts_and_related_system_files.html)

[https://askubuntu.com/questions/639990/what-is-the-group-id-of-this-group-name](https://askubuntu.com/questions/639990/what-is-the-group-id-of-this-group-name)

[https://www.cyberciti.biz/faq/understanding-etcpasswd-file-format/](https://www.cyberciti.biz/faq/understanding-etcpasswd-file-format/)

[https://www.cyberciti.biz/faq/understanding-etcshadow-file/](https://www.cyberciti.biz/faq/understanding-etcshadow-file/)

[https://en.wikipedia.org/wiki/Unix_time](https://en.wikipedia.org/wiki/Unix_time)

[https://www.cyberciti.biz/faq/understanding-etcgroup-file/](https://www.cyberciti.biz/faq/understanding-etcgroup-file/)

[https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/4/html/Introduction_To_System_Administration/s3-acctsgrps-gshadow.html](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/4/html/Introduction_To_System_Administration/s3-acctsgrps-gshadow.html)

[https://www.geeksforgeeks.org/chage-command-in-linux-with-examples/](https://www.geeksforgeeks.org/chage-command-in-linux-with-examples/)

.
