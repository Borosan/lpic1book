# 108.2. System logging

**Weight: **3

**Description:** Candidates should be able to configure the syslog daemon. This objective also includes configuring the logging daemon to send log output to a central log server or accept log output as a central log server. Use of the systemd journal subsystem is covered. Also, awareness of rsyslog and syslog-ng as alternative logging systems is included.

**Key Knowledge Areas:**

* Configuration of the syslog daemon
* Understanding of standard facilities, priorities and actions
* Configuration of logrotate
* Awareness of rsyslog and syslog-ng

**Terms and Utilities:**

* syslog.conf
* syslogd
* klogd
* /var/log/
* logger
* logrotate
* /etc/logrotate.conf
* /etc/logrotate.d/
* journalctl
* /etc/systemd/journald.conf
* /var/log/journal/

#### Why Logging?

A Linux system has many subsystems and applications running. We use system logging to gather data about our running system from the moment it boots. Sometimes we just need to know that all is well. At other times we use this data for auditing, debugging, knowing when a disk or other resource is running out of capacity, and many other purposes. 

### syslog

 Syslog is a standard for sending and receiving notification messages–in a particular format–from various network devices. The messages include time stamps, event messages, severity, host IP addresses, diagnostics and more.

### syslogd

The syslog daemon is a server process that provides a message logging facility for application and system processes.Unfortunately linux logging is one of aspects of linux which is transition fase. The traditional syslog facility and its syslogd daemon  has been supplemented by other logging facilities such as rsyslog, syslog-ng, and the systemd journal subsystem.

### syslog.conf

 The syslog.conf file is the main configuration file for the **syslogd. **Whenever syslogd   receives a log message, it acts based on the message's type (or facility) and its priority (called selector fields).

```
type (facility).priority (severity)  destination(where to send the log)
```

**Facilities** are simply categories. Some facilities in Linux are: **`auth, user, kern, cron, daemon, mail, local1, local2, ...`**

* **`auth`**` `: Security/authentication messages
* **`user `**: User-level messages
* **`kern `**: Kernel messages
* **`corn `**: Clock daemon
* **`daemon `**: System daemons
* **`mail `**: Mail system
* **`local0 – local7`** : Locally used facilities

**priorities **Unlike facilities, which have no relationship to each other, priorities are hierarchical. Possible priorities in Linux are:**` emerg/panic, alert, crit, err/error, warn/warning, notice, info, debug`**

1. **`emerg : `**System is unusable
2. **`alert : `**Action must be taken immediately
3. **`critical : `**Critical conditions
4. **`err : `**Error conditions
5. **`warning : `**Warning conditions
6. **`notice : `**Normal but significant conditions
7. **`info : `**Informational messages 
8. **`debug : `**Debug-level messages

> if we log some specific priority , all the more important things will be logged too

**Action:** Each line in this file specifies one or more facility/priority selectors followed by an action . On the action field we can have things like:

| action   | example           | notes                                                                                                  |
| -------- | ----------------- | ------------------------------------------------------------------------------------------------------ |
| filename | /var/log/messages | Writes logs to specified file                                                                          |
| username | user2             | Will notify that person on the screen                                                                  |
| @ip      | @192.168.10.42    | Will send logs to specified log server and that server decides how to treat logs based on its configs. |

 In the following syslog.conf line, mail.notice is the selector and /var/log/mail is the action (i.e., “write messages to /var/log/mail”):

```
mail.notice     /var/log/mail
```

Within the selector, “mail” is the facility (message category) and “notice” is the level of priority. You can see part of syslog.conf (CentOS6)  :

```
#### RULES ####

# Log all kernel messages to the console.
# Logging much else clutters up the screen.
#kern.*                                                 /dev/console

# Log anything (except mail) of level info or higher.
# Don't log private authentication messages!
*.info;mail.none;authpriv.none;cron.none                /var/log/messages

# The authpriv file has restricted access.
authpriv.*                                              /var/log/secure

# Log all the mail messages in one place.
mail.*                                                  -/var/log/maillog


# Log cron stuff
cron.*                                                  /var/log/cron

# Everybody gets emergency messages
*.emerg                                                 *

# Save news errors of level crit and higher in a special file.
uucp,news.crit                                          /var/log/spooler

# Save boot messages also to boot.log
local7.*                                                /var/log/boot.log

```

> `*`: wildcard .  signifying “any facility” or "any priority"
>
> dash - : means it can use memory cache (:don't waist time constantly writing to the disk )
>
> equal sign = : to log ONLY one specific level of priority.` facility.=priority action`

There is also  /etc/rsyslog.d/ directory and it is better for different softwares and admins to add their specific configs there, instead of editing the main configuration file (See Ubuntu16).

### klogd

 How do boot-time kernel messages get logged before a file system is even mounted? The kernel stores messages in a ring buffer in memory. The `klogd` daemon processes these messages directly to a console, or a file such as /var/log/dmesg, or through the syslog facility.

### /var/log

Almost all logfiles are located under /var/log directory and its sub-directories on Linux(CentOS6).

```
[root@centos6-1 ~]# ls /var/log
anaconda.ifcfg.log    cron              messages-20200217  tallylog
anaconda.log          cron-20200217     ntpstats           vmware-caf
anaconda.program.log  cups              pm-powersave.log   vmware-install.log
anaconda.storage.log  dmesg             ppp                vmware-tools-upgrader.log
anaconda.syslog       dmesg.old         prelink            vmware-vgauthsvc.log.0
anaconda.xlog         dracut.log        sa                 vmware-vmsvc.log
anaconda.yum.log      gdm               samba              wpa_supplicant.log
audit                 httpd             secure             wtmp
boot.log              lastlog           secure-20200217    Xorg.0.log
btmp                  maillog           spice-vdagent.log  Xorg.0.log.old
btmp-20200217         maillog-20200217  spooler            yum.log
ConsoleKit            messages          spooler-20200217   yum.log-20200217
```

You can use your favorite text editor or less or tail commands in conjunction with grep to read these log files.

### creating rsyslog listener

We can creating rsyslog listener and catch other systems log messages. That is pretty easy. 

```
###CentOS 6
vi /etc/rsyslog.conf

###Change from 
#$UDPServerRun 514
#$ModLoad imtcp
#$InputTCPServerRun 514

### to
$UDPServerRun 514
$ModLoad imtcp
$InputTCPServerRun 514
```

```
###ubuntu 16
vim /etc/default/rsyslog

### Change From
RSYSLOGD_OPTIONS=""

### To
RSYSLOGD_OPTIONS="-r"
```

and finally do not forget to restart the service `systemctl restart rsyslog` .

### journalctl

Systemd also has its own  journaling program called **journald  **and it stores things in binary files. We can't go and see text files (like what we did in syslog/rsyslog), so  we have to use special tool called journalctl to access them(CentOS7):

```
[root@centos7-1 ~]# journalctl 
-- Logs begin at Mon 2020-02-10 02:51:48 EST, end at Tue 2020-02-18 09:19:13 EST. --
Feb 10 02:51:48 localhost.localdomain systemd-journal[106]: Runtime journal is using 8.
Feb 10 02:51:48 localhost.localdomain kernel: Initializing cgroup subsys cpuset
Feb 10 02:51:48 localhost.localdomain kernel: Initializing cgroup subsys cpu
Feb 10 02:51:48 localhost.localdomain kernel: Initializing cgroup subsys cpuacct
Feb 10 02:51:48 localhost.localdomain kernel: Linux version 3.10.0-693.el7.x86_64 (buil
Feb 10 02:51:48 localhost.localdomain kernel: Command line: BOOT_IMAGE=/vmlinuz-3.10.0-
Feb 10 02:51:48 localhost.localdomain kernel: Disabled fast string operations
Feb 10 02:51:48 localhost.localdomain kernel: e820: BIOS-provided physical RAM map:
Feb 10 02:51:48 localhost.localdomain kernel: BIOS-e820: [mem 0x0000000000000000-0x0000
Feb 10 02:51:48 localhost.localdomain kernel: BIOS-e820: [mem 0x000000000009ec00-0x0000
Feb 10 02:51:48 localhost.localdomain kernel: BIOS-e820: [mem 0x00000000000dc000-0x0000
```

As we mentioned earlier ,  linux logging is one of aspects of linux which is in  under change. Distributions with systemd has journald, beside that some of them still preserve rsyslog and some other not. Try to find out your linux logging system

### /etc/systemd/journald.conf

The config file of journalctl is located at /etc/systemd/journald.conf (CentOS7)

```
[root@centos7-1 ~]# cat  /etc/systemd/journald.conf 
#  This file is part of systemd.
#
#  systemd is free software; you can redistribute it and/or modify it
#  under the terms of the GNU Lesser General Public License as published by
#  the Free Software Foundation; either version 2.1 of the License, or
#  (at your option) any later version.
#
# Entries in this file show the compile time defaults.
# You can change settings by editing this file.
# Defaults can be restored by simply deleting this file.
#
# See journald.conf(5) for details.

[Journal]
#Storage=auto
#Compress=yes
#Seal=yes
#SplitMode=uid
#SyncIntervalSec=5m
#RateLimitInterval=30s
#RateLimitBurst=1000
#SystemMaxUse=
#SystemKeepFree=
#SystemMaxFileSize=
#RuntimeMaxUse=
#RuntimeKeepFree=
#RuntimeMaxFileSize=
#MaxRetentionSec=
#MaxFileSec=1month
#ForwardToSyslog=yes
#ForwardToKMsg=no
#ForwardToConsole=no
#ForwardToWall=yes
#TTYPath=/dev/console
#MaxLevelStore=debug
#MaxLevelSyslog=debug
#MaxLevelKMsg=notice
#MaxLevelConsole=info
#MaxLevelWall=emerg
```

### logger

The Linux logger command provides an easy way to generate some logs(centOS6)

```
[root@centos6-1 ~]# logger local1.emerg Hello! This is my log!
```

 and it will appear at /var/log/syslog (or /var/log/messages):

```
[root@centos6-1 ~]# tail -5 /var/log/messages
Feb 18 06:54:42 server1 NetworkManager[2183]: <info>   nameserver '172.16.43.2'
Feb 18 06:54:42 server1 NetworkManager[2183]: <info>   domain name 'localdomain'
Feb 18 06:54:42 server1 dhclient[29269]: bound to 172.16.43.137 -- renewal in 693 seconds.
Feb 18 06:54:42 server1 nm-dispatcher.action: Script '/etc/NetworkManager/dispatcher.d/13-named' exited with error status 1.
Feb 18 06:59:34 server1 payam: Hello! This is my log!

```

### logrotate

 With the amount of logging that is possible, we need to be able to control the size of log files. This is done using the `logrotate`utility , which is usually run as a cron job.

The important files to pay attention to are:

* /usr/sbin/logrotate -- the logrotate command itself (the executable)
* /etc/cron.daily/logrotate -- the shell script that runs logrotate on a daily basis (note that it might be /etc/cron.daily/logrotate.cron on some systems)
* /etc/logrotate.conf -- the log rotation configuration file

Another important file is /etc/logrotate.d, included in the process through this line in the /etc/logrotate.conf file:

### /etc/logrotate.conf

Use the /etc/logrotate.conf configuration file to specify how your log rotating and archiving should happen.

```
[root@centos6-1 ~]# cat /etc/logrotate.conf 
# see "man logrotate" for details
# rotate log files weekly
weekly

# keep 4 weeks worth of backlogs
rotate 4

# create new (empty) log files after rotating old ones
create

# use date as a suffix of the rotated file
dateext

# uncomment this if you want your log files compressed
#compress

# RPM packages drop log rotation information into this directory
include /etc/logrotate.d

# no packages own wtmp and btmp -- we'll rotate them here
/var/log/wtmp {
    monthly
    create 0664 root utmp
	minsize 1M
    rotate 1
}

/var/log/btmp {
    missingok
    monthly
    create 0600 root utmp
    rotate 1
}

# system-specific logs may be also be configured here.
```

Each log file may be handled daily, weekly, monthly, or when it grows too large. 

| parameter                    | meaning                                                                                                                         |
| ---------------------------- | ------------------------------------------------------------------------------------------------------------------------------- |
| missingok                    | don’t write an error message if the log file is missing                                                                         |
| daily, weekly, monthly       | rotate logs daily, weekly, monthly                                                                                              |
| rotate _N_                   | keep the latest _N_ logs and delete the older ones                                                                              |
| compress                     | compress the log (creates  gz files)                                                                                            |
|  **create** mode owner group |  Immediately after rotation (before the **postrotate** script is run) the log file is created with this acces and owner         |
| minsize N                    | Log files are rotated when they grow bigger than size bytes, but not before the additionally specified time interval(daily,...) |

this file contains some default settings and sets up rotation for a few logs that are not owned by any system packages. It also uses an `include` statement to pull in configuration from any file in the `/etc/logrotate.d` directory(CentOS6).

### /etc/logrotate.d

Any packages we install that need help with log rotation will place their Logrotate configuration here.

```
[root@centos6-1 ~]# ls /etc/logrotate.d/
ConsoleKit  cups  dracut  httpd  named  ppp  psacct  syslog  wpa_supplicant  yum

[root@centos6-1 ~]# cat /etc/logrotate.d/httpd 
/var/log/httpd/*log {
    missingok
    notifempty
    sharedscripts
    delaycompress
    postrotate
        /sbin/service httpd reload > /dev/null 2>/dev/null || true
    endscript
}
```

These are the meaning of some of these parameters:

| parameter      | meaning                                                                           |
| -------------- | --------------------------------------------------------------------------------- |
| missingok      | don’t write an error message if the log file is missing                           |
| notifempty     | don’t rotate the log file if it is empty.                                         |
| shared scripts |  Run **prerotate** and **postrotate** scripts for every log file which is rotated |
| delaycompress  | Postpone compression of the previous log file to the next rotation cycle          |

That's all!

.

.

.

.

[https://developer.ibm.com/tutorials/l-lpic1-108-2/](https://developer.ibm.com/tutorials/l-lpic1-108-2/)

[https://stackify.com/syslog-101/](https://stackify.com/syslog-101/)

[https://www.ibm.com/support/knowledgecenter/en/SSB23S\_1.1.0.15/gtpc1/hsyslog.html](https://www.ibm.com/support/knowledgecenter/en/SSB23S\_1.1.0.15/gtpc1/hsyslog.html)

[https://linux.die.net/man/5/syslog.conf](https://linux.die.net/man/5/syslog.conf)

[https://www.linuxjournal.com/article/5476](https://www.linuxjournal.com/article/5476)

[https://jadi.gitbooks.io/lpic1/content/1082\_system_logging.html](https://jadi.gitbooks.io/lpic1/content/1082\_system_logging.html)

[https://en.wikipedia.org/wiki/Syslog](https://en.wikipedia.org/wiki/Syslog)

[https://renenyffenegger.ch/notes/Linux/logging/klogd/index](https://renenyffenegger.ch/notes/Linux/logging/klogd/index)

[https://www.tecmint.com/create-centralized-log-server-with-rsyslog-in-centos-7/](https://www.tecmint.com/create-centralized-log-server-with-rsyslog-in-centos-7/)

[https://www.digitalocean.com/community/tutorials/how-to-manage-logfiles-with-logrotate-on-ubuntu-16-04](https://www.digitalocean.com/community/tutorials/how-to-manage-logfiles-with-logrotate-on-ubuntu-16-04)

[https://linux.die.net/man/8/logrotate](https://linux.die.net/man/8/logrotate)

[https://www.digitalocean.com/community/tutorials/how-to-manage-logfiles-with-logrotate-on-ubuntu-16-04](https://www.digitalocean.com/community/tutorials/how-to-manage-logfiles-with-logrotate-on-ubuntu-16-04)

.
