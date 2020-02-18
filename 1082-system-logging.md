# 108.2. System logging

**Weight:** 3

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

 The syslog.conf file is the main configuration file for the **syslogd.** Whenever syslogd   receives a log message, it acts based on the message's type \(or facility\) and its priority.

* **Facilities** are simply categories. Some facilities in Linux are: **`auth, user, kern, cron, daemon, mail, user, local1, local2, ...`**
* **priorities** Unlike facilities, which have no relationship to each other, priorities are hierarchical. Possible priorities in Linux are: **`emerg/panic, alert, crit, err/error, warn/warning, notice, info, debug`**

Each line in this file specifies one or more facility/priority selectors followed by an action. On the action field we can have things like:

| action | example | notes |
| :--- | :--- | :--- |
| filename | /var/log/messages | Writes logs to specified file |
| username | user2 | Will notify that person on the screen |
| @ip | @192.168.10.42 | Will send logs to specified log server and that server decides how to treat logs based on its configs. |

 















.

.

.

[https://developer.ibm.com/tutorials/l-lpic1-108-2/](https://developer.ibm.com/tutorials/l-lpic1-108-2/)

[https://stackify.com/syslog-101/](https://stackify.com/syslog-101/)

[https://www.ibm.com/support/knowledgecenter/en/SSB23S\_1.1.0.15/gtpc1/hsyslog.html](https://www.ibm.com/support/knowledgecenter/en/SSB23S_1.1.0.15/gtpc1/hsyslog.html)

[https://linux.die.net/man/5/syslog.conf](https://linux.die.net/man/5/syslog.conf)

[https://www.linuxjournal.com/article/5476](https://www.linuxjournal.com/article/5476)

[https://jadi.gitbooks.io/lpic1/content/1082\_system\_logging.html](https://jadi.gitbooks.io/lpic1/content/1082_system_logging.html)

.

