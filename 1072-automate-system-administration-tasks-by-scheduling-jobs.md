# 107.2. Automate system administration tasks by scheduling jobs

**Weight:** 4

Description: Candidates should be able to use cron or anacron to run jobs at regular intervals and to use at to run jobs at a specific time.

**Key Knowledge Areas:**

* Manage cron and at jobs
* Configure user access to cron and at services
* Configure anacron

**Terms and Utilities:**

* /etc/cron.{d,daily,hourly,monthly,weekly}/
* /etc/at.deny
* /etc/at.allow
* /etc/crontab
* /etc/cron.allow
* /etc/cron.deny
* /var/spool/cron/
* crontab
* at
* atq
* atrm
* anacron
* /etc/anacrontab

Many system administration tasks must be done regularly, such as rotating log files, backing up files or databases, preparing reports, or installing system updates. In this lesson we will learn how to automate these kinds of jobs by setting up  scheduling for them

By scheduling :

* We can start a job for a time at which system usage is low
* Errors will be reduced due to less manual interaction is needed.
* We are  sure that  jobs  always run the same way
* System administrators can sleep more!

### Run jobs at regular intervals <a href="run-jobs-at-regular-intervals" id="run-jobs-at-regular-intervals"></a>

Linux systems have two facilities for scheduling jobs to run at regular intervals:

* The original `cron` facility is best suited to servers and systems that are continuously powered on.
* The `anacron` (or anachronistic `cron`) facility is suited to systems such as desktops or laptops that can be asleep or running on battery power.

| cron                                                 | anacron                                |
| ---------------------------------------------------- | -------------------------------------- |
| Good for servers                                     | Good for laptops and desktops          |
| Granularity from one minute to one year              | Daily, weekly, and monthly granularity |
| Job runs only if system is running at scheduled time | Job runs when system is next available |
| Can be used by normal users                          | Needs root authority                   |

### Schedule periodic jobs with cron <a href="schedule-periodic-jobs-with-cron" id="schedule-periodic-jobs-with-cron"></a>

 The `cron` facility consists of the `cron` daemon and a set of tables that describe what work is to be done and with what frequency. The `cron` daemon is usually started by the `init`, `upstart`, or `systemd` process at system startup. 

### crontab file syntax and Operators

Crontab (cron table) is a text file that specifies the schedule of cron jobs. The `cron` daemon wakes up every minute and checks each crontab for jobs that need to run. 

 There are two types of crontab files.  The  individual user crontab files and  system-wide crontab files.

#### crontab syntax and operators

Each line in the user crontab file contains six fields separated by a space followed by the command to be run.

```
* * * * * command(s)
- - - - -
| | | | |
| | | | ----- Day of week (0 - 7) (Sunday=0 or 7)
| | | ------- Month (1 - 12)
| | --------- Day of month (1 - 31)
| ----------- Hour (0 - 23)
------------- Minute (0 - 59)
```

* `*` -The asterisk operator means any value or always.
* `,` -The comma operator allows you to specify a list of values for repetition. 
* `-` -The hyphen operator allows you to specify a range of values. 
* `/` -The slash operator allows you to specify values that will be repeated over a certain interval between them.

> Also if you have `@reboot` or `@daily` instead of time fields, the command will be run once after the reboot or daily.

Lets see some examples:

```
@reboot      ### on reboot

50 13 * * *  ### 1:50 PM daily

40 6 4 * *   ### 4th of every month at 6:40AM

05 * * 1 0   ### 5 mintues past everyhour,each Sunday in January

0 15 29 11 5 ### 3:00PM Every November 29th  that lands on a Friday

1,5,10 * * * * # 1,5,10 mintues past every hour

30 20 * * 1-5 ## weekdays at 8:20 PM

*/5 * * * *  ### ever 5 mintues
```

## user specific crons

### /var/spool/cron/

 Users crontab files are stored by the user’s name, and their location varies by operating systems. In Red Hat based system such as CentOS, crontab files are stored in the `/var/spool/cron` directory while on Debian and Ubuntu files are stored in the `/var/spool/cron/crontabs` directory.

Although you can edit the user crontab files manually, it is recommended to use the `crontab` command.

### crontab command

The crontab command allows you to install or open a crontab file for editing.

You can use the crontab command to view, add, remove, or modify cron jobs using the following options:

* `crontab -e` : Edit crontab file, or create one if it doesn’t already exist.
* `crontab -l` : Display crontab file contents.
* `crontab -r` : Remove your current crontab file.
* `crontab -i` :Remove your current crontab file with a prompt before removal.
* `crontab -u <username>` : Edit other user crontab file. Requires system administrator privileges.

```
user1@ubuntu16-1:~$ crontab -e
no crontab for user1 - using an empty one

Select an editor.  To change later, run 'select-editor'.
  1. /bin/ed
  2. /bin/nano        <---- easiest
  3. /usr/bin/vim.basic
  4. /usr/bin/vim.tiny

Choose 1-4 [2]: 3
```

The crontab  -e command opens the crontab file using the editor specified by the `VISUAL` or `EDITOR` environment variables.

```
# Edit this file to introduce tasks to be run by cron.
# 
# Each task to run has to be defined through a single line
# indicating with different fields when the task will be run
# and what command to run for the task
# 
# To define the time you can provide concrete values for
# minute (m), hour (h), day of month (dom), month (mon),
# and day of week (dow) or use '*' in these fields (for 'any').# 
# Notice that tasks will be started based on the cron's system
# daemon's notion of time and timezones.
# 
# Output of the crontab jobs (including errors) is sent through
# email to the user the crontab file belongs to (unless redirected).
# 
# For example, you can run a backup of all your user accounts
# at 5 a.m every week with:
# 0 5 * * 1 tar -zcf /var/backups/home.tgz /home/
# 
# For more information see the manual pages of crontab(5) and cron(8)
# 
# m h  dom mon dow   command
~                                                                               
"/tmp/crontab.f5VKYq/crontab" 22L, 888C                       1,1           All
```

as an example add bellow line to above and it would send and email every 5 mintues:

```
5 * * * * echo "Hello" | mail -s "Cron Test" user1@localhost.com
```

crontab -e also check the syntax before exiting the file , which is really helpful.

crontab -l would show the above contents. Lets check if user crontab file has been created:

```
root@ubuntu16-1:/var/spool/cron/crontabs# ls -l
total 4
-rw------- 1 user1 crontab 1154 Feb 15 05:07 user1
```

## system wide cron

 In addition to the user crontab files in /var/spool/cron, the `cron` daemon also checks /etc/crontab and any crontabs in the /etc/cron.d directory. 

### /etc/crontab , /etc/cron.d

 `/etc/crontab` and the files inside the `/etc/cron.d` directory are system-wide **crontab** files that can be edited only by the system administrators.

>  /etc/crontab is  updated by direct editing. You cannot use the `crontab` command to update file files or files in the /etc/cron.d directory.

#### System-wide Crontab Files <a href="system-wide-crontab-files" id="system-wide-crontab-files"></a>

The syntax of system-wide crontab files is slightly different than user crontabs. It contains an additional mandatory user field that specifies which user will run the cron job.

```
* * * * * <username> command(s)
```

This file should be edited with an editor directly and we can mention which user runs command(s).

```
# /etc/crontab: system-wide crontab
# Unlike any other crontab you don't have to run the `crontab'
# command to install the new version when you edit this file
# and files in /etc/cron.d. These files also have username fields,
# that none of the other crontabs do.

SHELL=/bin/sh
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin

# m h dom mon dow user  command
17 *    * * *   root    cd / && run-parts --report /etc/cron.hourly
25 6    * * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.daily )
47 6    * * 7   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.weekly )
52 6    1 * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.monthly )
#
~                                                                               
~                                                                               
~                                                                                                                                                                                                                                         
"/etc/crontab" 15L, 722C                                      14,27-35      All
```

### /etc/cron.{daily,hourly,monthly,weekly}/

In most Linux distributions you can also put **scripts** inside the `/etc/cron.{hourly,daily,weekly,monthly}` directories and the scripts will be executed every `hour/day/week/month`.

```
root@ubuntu16-1:~# tree /etc/cron*
/etc/cron.d
├── anacron
├── php
└── popularity-contest
/etc/cron.daily
├── 0anacron
├── apache2
├── apport
├── apt-compat
├── aptitude
├── bsdmainutils
├── cracklib-runtime
├── dpkg
├── logrotate
├── man-db
├── mlocate
├── passwd
├── popularity-contest
├── quota
├── samba
├── update-notifier-common
└── upstart
/etc/cron.hourly
/etc/cron.monthly
└── 0anacron
/etc/crontab [error opening dir]
/etc/cron.weekly
├── 0anacron
├── fstrim
├── man-db
└── update-notifier-common

0 directories, 25 files
```

as an example lets take look at one of them:

```
root@ubuntu16-1:~# cat /etc/cron.daily/passwd 
#!/bin/sh

cd /var/backups || exit 0

for FILE in passwd group shadow gshadow; do
        test -f /etc/$FILE              || continue
        cmp -s $FILE.bak /etc/$FILE     && continue
        cp -p /etc/$FILE $FILE.bak && chmod 600 $FILE.bak
done
```

### anacron

The cron facility works well for systems that run continuously.If the system is down when the cron should run a task, that cron job wont run till the next occurrence! But anacron creates the timestamp each time a daily, weekly or monthly job runs. 

> Note: anacron checks the timestamps at BOOT TIME and does not handle jobs that must run hourly or every minute.

> ### /etc/anacron

 The table of jobs for `anacron` is stored in /etc/anacrontab, which has a slightly different format from /etc/crontab.

```
root@ubuntu16-1:/# cat /etc/anacrontab 
# /etc/anacrontab: configuration file for anacron

# See anacron(8) and anacrontab(5) for details.

SHELL=/bin/sh
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin
HOME=/root
LOGNAME=root

# These replace cron's entries
1	5	cron.daily	run-parts --report /etc/cron.daily
7	10	cron.weekly	run-parts --report /etc/cron.weekly
@monthly	15	cron.monthly	run-parts --report /etc/cron.monthly
```

>  just like/etc/crontab , /etc/anacrontab is updated by direct editing. 

{% hint style="info" %}
#### Anacrontab Format

```
period   delay   job-identifier   command
```

* ** period in days** : specifies the frequency of execution of a job in  _N_ days.
*  **delay in minutes**: number of minutes anacron should wait before executing the job after reboot.
*  **job-identifier :**It is the name for the job’s timestamp file. It should be unique for each job. This will be available as a file under the /var/spool/anacron directory.
*  **command**: specifies the command to execute. 
{% endhint %}

### /var/spool/anacron

 `anacron` keeps a time stamp file in /var/spool/anacron for each job to record when the job runs. When `anacron` runs, it checks to see if the required number of days has passed since a job last ran and runs the job if necessary.

```
root@ubuntu16-1:/# ls -l /var/spool/anacron/
total 12
-rw------- 1 root root 9 Feb 15 07:35 cron.daily
-rw------- 1 root root 9 Feb  1 07:45 cron.monthly
-rw------- 1 root root 9 Feb 10 00:46 cron.weekly
```

This file will contain a single line that indicates the last time when this job was executed.

```
root@ubuntu16-1:/# cat /var/spool/anacron/cron.daily 
20200215
```

### at

 Sometimes you need to run a job at a future time just once, rather than regularly. For this purpose you use the `at` command. (ubuntu: `apt install at`)

 A typical **at** command sequence looks like this

```
at 5:45
```

By running at command  It then places you at a special prompt, where you can type in the command (or series of commands) to be run at the scheduled time. When you're done, press **Control-D** on a new line, and your command will be placed in the queue.

```
user1@ubuntu16-1:~$ at 5:45
warning: commands will be executed using /bin/sh
at> touch BlaHBlaH 
at> <EOT>
job 3 at Sat Feb 15 05:45:00 2020

user1@ubuntu16-1:~$ ls -ltrh | grep -i blah
-rw-rw-r-- 1 user1 user1    0 Feb 15 05:45 BlaHBlaH
```

> warning: commands will be executed using /bin/sh

Some other examples of at command:

```
  Example                  Schedule Task at
-----------                -------------------
at 10:00 AM                at coming 10:00 AM
at 10:00 AM Sun            at 10:00 AM on coming Sunday
at 10:00 AM July 25        at 10:00 AM on coming 25’th July
at 10:00 AM 6/22/2019      at 10:00 AM on coming 22’nd June 2019
at 10:00 AM next month     at 10:00 AM on the same date at next month
at 10:00 AM tomorrow       at 10:00 AM tomorrow
at teatime                 to execute on next 4:00 pM
at midnight                to execute on next 12:00 AM
at now + 1 hour            to execute just after 1 hour
at now + 1 week            to execute just after 1 week
at now + 1 month           to execute just after 1 month
at now + 1 year            to execute just after 1 year
```

at command has other members in its family:

### atq

lists the pending jobs of users

```
user1@ubuntu16-1:~$ at 06:20
warning: commands will be executed using /bin/sh
at> touch file1
at> <EOT>
job 4 at Sat Feb 15 06:20:00 2020
user1@ubuntu16-1:~$ at 06:30
warning: commands will be executed using /bin/sh
at> touch file2
at> <EOT>
job 5 at Sat Feb 15 06:30:00 2020

user1@ubuntu16-1:~$ atq
4	Sat Feb 15 06:20:00 2020 a user1
5	Sat Feb 15 06:30:00 2020 a user1
```

### atrm 

 delete jobs by their job number

```
user1@ubuntu16-1:~$ atrm 5
user1@ubuntu16-1:~$ atq
4	Sat Feb 15 06:20:00 2020 a user1
```

  atq command only shows the list of jobs but if you want to check what script/commands are scheduled with that task use `at -c JobNum` command and see the last line.

{% hint style="info" %}
both cron and are system services.
{% endhint %}

## Configure user access to job scheduling

We can control access to the crontab command by using two files in the /etc/cron.d directory: cron.deny and cron.allow. These files permit only specified users to perform crontab command tasks such as creating, editing, displaying, or removing their own crontab files.

> The cron.deny and cron.allow files consist of a list of user names, one user name per line.

### /etc/cron.allow , /etc/cron.deny

These access control files work together as follows:

* If cron.allow exists, only the users who are listed in this file can create, edit, display, or remove crontab files.
* If cron.allow does not exist, all users can submit crontab files, except for users who are listed in cron.deny.
* If neither cron.allow nor cron.deny exists, superuser privileges are required to run the crontab command.

Superuser privileges are required to edit or create the cron.deny and cron.allow files.

```
root@ubuntu16-1:~# cat /etc/cron.deny
user2
```

###  /etc/at.allow , /etc/at.deny

The corresponding /etc/at.allow and /etc/at.deny files have similar effects for the `at` facility.

.

.

.

{% hint style="info" %}
### Crontab Variables <a href="crontab-variables" id="crontab-variables"></a>

The cron daemon automatically sets several environment variables.

* The default path is set to `PATH=/usr/bin:/bin`. If the command you are calling is present in the cron specified path, you can either use the absolute path to the command or change the cron `$PATH` variable. You can’t implicitly append `:$PATH` as you would do with a regular script.
* The default shell is set to `/bin/sh`. You can set a different shell by changing the `SHELL` variable.
* Cron invokes the command from the user’s home directory. The `HOME` variable can be overridden by settings in the crontab.
* The email notification is sent to the owner of the crontab. To overwrite the default behavior, you can use the `MAILTO` environment variable with a list (comma separated) of all the email addresses you want to receive the email notifications. If `MAILTO` is defined but empty (`MAILTO=""`), no mail is sent.
{% endhint %}

.

.

[https://developer.ibm.com/tutorials/l-lpic1-107-2/](https://developer.ibm.com/tutorials/l-lpic1-107-2/)

[https://linuxize.com/post/scheduling-cron-jobs-with-crontab/](https://linuxize.com/post/scheduling-cron-jobs-with-crontab/)

[https://jadi.gitbooks.io/lpic1/content/1072\_automate_system_administration_tasks_by_scheduling_jobs.html](https://jadi.gitbooks.io/lpic1/content/1072\_automate_system_administration_tasks_by_scheduling_jobs.html)

[https://www.thegeekdiary.com/centos-rhel-anacron-basics-what-is-anacron-and-how-to-configure-it/](https://www.thegeekdiary.com/centos-rhel-anacron-basics-what-is-anacron-and-how-to-configure-it/)

[https://www.thegeekstuff.com/2011/05/anacron-examples/](https://www.thegeekstuff.com/2011/05/anacron-examples/)

[https://www.computerhope.com/unix/uat.htm](https://www.computerhope.com/unix/uat.htm)

[https://tecadmin.net/one-time-task-scheduling-using-at-commad-in-linux/](https://tecadmin.net/one-time-task-scheduling-using-at-commad-in-linux/)

[https://docs.oracle.com/cd/E19253-01/817-0403/sysrescron-23/index.html](https://docs.oracle.com/cd/E19253-01/817-0403/sysrescron-23/index.html)

.
