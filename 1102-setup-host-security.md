# 110.2. Setup host security

**Weight:** 3

**Description:** Candidates should know how to set up a basic level of host security.

**Key Knowledge Areas:**

* Awareness of shadow passwords and how they work
* Turn off network services not in use
* Understand the role of TCP wrappers

**Terms and Utilities:**

* /etc/nologin
* /etc/passwd
* /etc/shadow
* /etc/xinetd.d/
* /etc/xinetd.conf
* /etc/inetd.d/
* /etc/inetd.conf
* /etc/inittab
* /etc/init.d/
* /etc/hosts.allow
* /etc/hosts.deny

We have previously talked about /etc/passwd , /etc/nologin and /etc/shadow. So lets review them quickly:

### /etc/passwd

 **/etc/passwd** file stores essential information, which required during login. In other words, it stores user account information. The /etc/passwd is a plain text file.

```text
root@ubuntu16-1:~# tail /etc/passwd
pulse:x:117:124:PulseAudio daemon,,,:/var/run/pulse:/bin/false
rtkit:x:118:126:RealtimeKit,,,:/proc:/bin/false
saned:x:119:127::/var/lib/saned:/bin/false
usbmux:x:120:46:usbmux daemon,,,:/var/lib/usbmux:/bin/false
payam:x:1000:1000:ubuntu16.04.3-1,,,:/home/payam:/bin/bash
user1:x:1001:1001::/home/user1:/bin/bash
sshd:x:121:65534::/var/run/sshd:/usr/sbin/nologin
mysql:x:122:129:MySQL Server,,,:/nonexistent:/bin/false
user3:x:1003:1003::/home/user3:
user2:x:1004:1004::/home/user2:/bin/bash
```

 Each line in /etc/passwd file represents an individual user account and contains following seven fields separated by colons \(**:**\)

1. Username or login name
2. Encrypted password
3. User ID
4. Group ID
5. User description
6. User’s home directory
7. User’s login shell

The /etc/passwd file should have general read permission as many command utilities use it to map user IDs to user names. However, write access to the /etc/passwd must only limit for the superuser/root account.

```text
root@ubuntu16-1:~# ls -l /etc/passwd
-rw-r--r-- 1 root root 2470 Mar 23 01:24 /etc/passwd
```

as we said, in old days /etc/passwd  was a place that  all users information even the user's password, and it caused security issues . To solve the problem /etc/shadow was invented.  An x character indicates that encrypted password is stored in /etc/shadow file.

### /etc/shadow

The /etc/shadow file contains encrypted passwords, along with password- and account-expiration information.

```text
root@ubuntu16-1:~# tail -5 /etc/shadow
user1:$6$c9PN.175$.t.CG0E0Gtr/trq4pqquSe1BemMjB6Zc3E0ExUOVufuTkPNe3BSRv3DyUuXFHPiAbEujzuSMCeMsCbpg8cV2j.:17749:0:99999:7:::
sshd:*:17749:0:99999:7:::
mysql:!:17867:0:99999:7:::
user3:$6$LPhxz61y$V0/HsC6SF6olabfoKuDKj3Gfm.wRVvbUI.GXWGJ2Icejs91ZQDgZHIAc153x4VYrJcg.oetNlwq.X7xxJIRDR0:18343:0:99999:7:::
user2:$6$sd2T1NDd$ihaP8mO7/rhSUFDccY3evqQqM/VwDVDYSQwZJstV6Dnjy3Me83faro6pk/Nb1GuvRqy8J66ZBHIGqIpIrzl5a0:18343:0:99999:7:::
```

 Each line in **/etc/shadow** file represents an individual user account and contains following nine fields separated by colons \(**:**\)

1. Username
2. Encrypted password
3. Date of last password change
4. Minimum required days between password changes
5. Maximum allowed days between password changes
6. Number of days in advance to display password expiration message
7. Number of days after password expiration to disable the account
8. Account expiration date
9. Reserve field

 Unlike **/etc/passwd** file, the **/etc/shadow** file is not world readable. It is readable only by the root user or super user. 

```text
root@ubuntu16-1:~# ls -l /etc/shadow
-rw-r----- 1 root shadow 1704 Mar 23 01:23 /etc/shadow
```

### /etc/nologin



turn off network services

If you are a sysadmin, you’ll deal with the service regularly

stopping network services not in use

super servers

tcp wrappers

.

.

.

[https://www.cyberciti.biz/faq/understanding-etcpasswd-file-format/](https://www.cyberciti.biz/faq/understanding-etcpasswd-file-format/)

[https://www.computernetworkingnotes.com/rhce-study-guide/etc-passwd-file-in-linux-explained-with-examples.html](https://www.computernetworkingnotes.com/rhce-study-guide/etc-passwd-file-in-linux-explained-with-examples.html)

[https://www.cyberciti.biz/faq/understanding-etcshadow-file/](https://www.cyberciti.biz/faq/understanding-etcshadow-file/)

[https://www.computernetworkingnotes.com/rhce-study-guide/etc-shadow-file-in-linux-explained-with-examples.html](https://www.computernetworkingnotes.com/rhce-study-guide/etc-shadow-file-in-linux-explained-with-examples.html)

.

