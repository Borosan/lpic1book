# 103.5. Create, monitor and kill processes

**Weight:** 4

**Description:** Candidates should be able to perform basic process management.

**Key Knowledge Areas:**

* Run jobs in the foreground and background
* Signal a program to continue running after logout
* Monitor active processes
* Select and sort processes for display
* Send signals to processes

**Terms and Utilities:**

* &
* bg
* fg
* jobs
* kill
* nohup
* ps
* top
* free
* uptime
* pgrep
* pkill
* killall
* screen

In this section we are talking about jobs and processes. What you should know is that, what exactly is a job?

If you run a command from the shell, that is job. 

```
root@ubuntu16-1:~# ls
client.conf  files-backup            openvpn.key  unzip_6.0-20ubuntu1_amd64.deb
dead.letter  jcal_0.4.1-2_amd64.deb  test-space   zip_3.0-11_amd64.deb
```

A job might be executed and exit immediately and runs for a short time only but some jobs run for a long time.

```
root@ubuntu16-1:~# sleep 3600

```

that goes to sleep for 3600 seconds, that is an hour! So you can wait for an hour (considering that you can not use your terminal) or you can run a job in background.

### Foreground and Background jobs (&)

 A **job is a process that the shell manages**. Each job is assigned a sequential job ID. Because a job is a process, each job has an associated PID. There are two types of job statuses:

1\. **Foreground**: When we enter a command in a terminal window, the command occupies that terminal window until it completes. This is a foreground job.\
 2\. **Background**: When we enter an ampersand (**&**) symbol at the end of a command line, the command runs without occupying the terminal window. The shell prompt is displayed immediately after you press Return. This is an example of a background job.

```
user1@ubuntu16-1:~$ sleep 5 &
[1] 68481
user1@ubuntu16-1:~$ 
[1]+  Done                    sleep 5
user1@ubuntu16-1:~$
```

> By default you get notified about terminated jobs when you hit enter ,Try set -b to get notified immediately.

**Job Control Commands**

Job control commands enable **us **to place jobs in the foreground or background, and to start or stop jobs. The table describes the job control commands.

| Command   | Description                                                                    |
| --------- | ------------------------------------------------------------------------------ |
| jobs      | Lists all jobs                                                                 |
| bg %n     | Places the current or specified job in the background, where n is the job ID   |
| fg %n     | Brings the current or specified job into the foreground, where n is the job ID |
| Control-Z | Stops the foreground job and places it in the background as a stopped job      |
| Control-C | Ctrl+C kills the process                                                       |

> if no job id is mentioned bg and fg consider the most recent job

### jobs

```
root@ubuntu16-1:~# jobs
[1]+  Running                 sleep 1000 &
root@ubuntu16-1:~# sleep 2000 &
[2] 54832
root@ubuntu16-1:~# jobs
[1]-  Running                 sleep 1000 &
[2]+  Running                 sleep 2000 &
root@ubuntu16-1:~# sleep 3000
^Z
[3]+  Stopped                 sleep 3000
root@ubuntu16-1:~# jobs
[1]   Running                 sleep 1000 &
[2]-  Running                 sleep 2000 &
[3]+  Stopped                 sleep 3000
```

### bg

```
root@ubuntu16-1:~# bg
[3]+ sleep 3000 &
root@ubuntu16-1:~# jobs
[1]   Running                 sleep 1000 &
[2]-  Running                 sleep 2000 &
[3]+  Running                 sleep 3000 &
```

we cloud use `fg %3` command and it would have the same result. 

### fg

```
root@ubuntu16-1:~# jobs
[1]   Running                 sleep 1000 &
[2]-  Running                 sleep 2000 &
[3]+  Running                 sleep 3000 &
root@ubuntu16-1:~# fg %2
sleep 2000
^C
root@ubuntu16-1:~# jobs
[1]-  Running                 sleep 1000 &
[3]+  Running                 sleep 3000 &
root@ubuntu16-1:~# 
```

We brought a job to foreground and use Ctrl + C to kill that job. 

> we can disable and enable shell job control feature  with`set +m and set -m` commands.

jobs normally stick to the shell running it, so they are killed when we close the terminal or log out. Some times we need to make sure that the running job is not attached to the running shell.This is where nohup and disown come to play.

{% hint style="info" %}
**huponexit**

jobs are killed **only if** the `huponexit` option is set! 

run`shopt huponexit` to see if this is true.

* If `huponexit` is false, _which **is the default** on at least some linuxes these days_, then backgrounded jobs will **not** be killed on normal logout!
* If `huponexit` is true, then we can use `nohup` or `disown` to dissociate the process from the shell so it does not get killed when you exit. Or, run things with `screen`.

use `shopt -s huponexit` and `shopt -u huponexit` in order to set and unset it.
{% endhint %}

### Signal a program to continue running after logout

### nohup

`Nohup` stands for** no hangup**, and that means even if the parent shell is diconnected the job just will continue,  The output of the **nohup** command will write in **nohup.out** the file if any redirecting filename is not mentioned in **nohup** command.

```
nohup command [command-argument ...]
```

Using nohup with commands:

```
root@ubuntu16-1:~/test-space# nohup ping  8.8.8.8 &
[2] 55210
root@ubuntu16-1:~/test-space# nohup: ignoring input and appending output to 'nohup.out'

root@ubuntu16-1:~/test-space# ls 
nohup.out
root@ubuntu16-1:~/test-space# head -3 nohup.out 
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=128 time=97.2 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=128 time=74.4 ms
```

Now even if we close the terminal and open it again ping is still running.

```
root@ubuntu16-1:~/test-space# jobs
root@ubuntu16-1:~/test-space# ps aux | grep ping | grep -v grep
root      55210  0.0  0.0   6536   756 ?        S    02:32   0:00 ping 8.8.8.8
root@ubuntu16-1:~/test-space# 
```

> Please notice that we can not use `fg`, `bg` commands on that particular job anymore.

 When we run nohup command without ‘**&’** then it returns to shell command prompt immediately after running that particular command in the background.

> We useually use nohup with output redirection `nohup bash script.sh > myresult.txt 2>&1`

{% hint style="info" %}
**disown**

We can also use disown command, it is used after the a process has been launched and put in the background, it’s work is to remove a shell job from the shell’s active list jobs, therefore we can not use fg, bg commands on that particular job anymore but that process keeps running even after closing current shell.

```
root@ubuntu16-1:~# jobs
root@ubuntu16-1:~# sleep 111 &
[1] 6394
root@ubuntu16-1:~# jobs -l
[1]+   Running                 sleep 111 &
root@ubuntu16-1:~# disown %1
root@ubuntu16-1:~# jobs 
root@ubuntu16-1:~# pgrep sleep
6394
```

 disown -h %1 lets us to have  normal job control mechanisms to continue controlling the process until closing the terminal.
{% endhint %}

| Option | Description                               |
| ------ | ----------------------------------------- |
| **-a** | Delete all jobs if jobID is not supplied. |
| **-r** | Delete only running jobs.                 |

There is a problem with nohup and disown commands. There is no way to bring back that job to forground and work interactivly with that. So we need a different solution, screen.

### screen

 **screen** command in Linux provides the ability to launch and use multiple shell sessions from a single _ssh_ session. 

When a process is started with ‘screen’, the process can be detached from session and then can reattach the session at a later time. When the session is detached, the process that was originally started from the screen is still running and managed by the screen itself. The process can then re-attach the session at a later time, and the terminals are still there, the way it was left. (you might need to install it)

#### Start screen for the first time

simple use screen command ** :**

```
root@ubuntu16-1:~# screen
```

```
Screen version 4.03.01 (GNU) 28-Jun-15

Copyright (c) 2010 Juergen Weigert, Sadrul Habib Chowdhury
Copyright (c) 2008, 2009 Juergen Weigert, Michael Schroeder, Micah Cowan,
Sadrul Habib Chowdhury
Copyright (c) 1993-2002, 2003, 2005, 2006, 2007 Juergen Weigert, Michael
Schroeder
Copyright (c) 1987 Oliver Laumann

This program is free software; you can redistribute it and/or modify it under
the terms of the GNU General Public License as published by the Free Software
Foundation; either version 3, or (at your option) any later version.

This program is distributed in the hope that it will be useful, but WITHOUT
ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS
FOR A PARTICULAR PURPOSE. See the GNU General Public License for more details.

You should have received a copy of the GNU General Public License along with
this program (see the file COPYING); if not, see http://www.gnu.org/licenses/,
or contact Free Software Foundation, Inc., 51 Franklin Street, Fifth Floor,

                  [Press Space for next page; Return to end.]
```

use` screen -s Session_Name` to start a named session.now lets run a command inside screen:

```
root@ubuntu16-1:~# sleep 1111

```

> Inorder to create a new screen inside the current screen (nested screen) use  just press **Ctrl-a** +**c**

#### Detach the screen

One of the advantages of screen that is you can detach it. Then, you can restore it without losing anything you have done on the screen. use  **Ctrl-a + d **to detach**:**

```
root@ubuntu16-1:~# sleep 1111

```

```
root@ubuntu16-1:~# screen
[detached from 56798.pts-4.ubuntu16-1]
root@ubuntu16-1:~# 
```

 **-d** is** also **used to detach a screen session so that it can be reattached in future.

**List screens**

 **screen  -ls** is used to display the currently opened screens including those running in the background:

```
root@ubuntu16-1:~# screen -list
There is a screen on:
	56798.pts-4.ubuntu16-1	(10/14/2019 04:15:00 AM)	(Detached)
1 Socket in /var/run/screen/S-root.
```

**Reattach to a screen**

 **-r ** It is used to reattach a screen session which was detached in past:

```
root@ubuntu16-1:~# screen -r 56798
```

```
root@ubuntu16-1:~# sleep 1111
root@ubuntu16-1:~# 

```

> We usually use `screen -dr <Screen-ID>` command.This means detach the specified screen first and then reattach it. 

#### Switching between screens

When we do nested screen, you can switch between screen using command **Ctrl-a +n**. It will be move to the next screen. When  need to go to the previous screen, just press **Ctrl-a** +**p**.

#### Terminate screen session

Use  “**Ctrl-A**” and “**K**” to kill the screen.



### Monitor active processes

#### What is process?

A program is a series of instructions that tell the computer what to do. When we run a program, those instructions are copied into memory and space is allocated for variables and other stuff required to manage its execution. This running instance of a program is called a process and it's processes which we manage

Each process got a **PID**. PID stands for  **process identifier** and it is is a unique number that identifies each of the running processes in an operating system

processes can further be categorized into:

* **Parent processes** – these are processes that create other processes during run-time.
* **Child processes** – these processes are created by other processes during run-time.

therefore **PPID** stands for **Parent Process ID**. notes:

* note1:Used up pid’s can be used in again for a newer process since all the possible combinations are used.
* note2:At any point of time, no two processes with the same pid exist in the system because it is the pid that Unix uses to track each process.

> If we use the `jobs` command with the `-l` option, it will also show process ID.  

### ps

ps (Process status) can be used to see/list all the running processes and their PIDs along with some other information depends on different options.

```
ps [options]
```

 ps reads the process information from the virtual files in **/proc** file-system.  In it's simplest form, when used without any option, `ps` will print four columns of information for minimum two processes running in the current shell, the shell itself, and the processes that run in the shell when the command was invoked.

```
root@ubuntu16-1:~# ps
   PID TTY          TIME CMD
 57301 pts/18   00:00:00 bash
 59076 pts/18   00:00:00 ps
```

 Where,\
 **PID –** the unique process ID\
 **TTY –** terminal type that the user is logged into\
 **TIME –** amount of CPU in minutes and seconds that the process has been running\
 **CMD –** name of the command that launched the process.

>  **Note –** Sometimes when we execute **ps** command, it shows TIME as 00:00:00. It is nothing but the total accumulated CPU utilization time for any process and 00:00:00 indicates no CPU time has been given by the kernel till now. In above example we found that, for bash no CPU time has been given. This is because bash is just a parent process for different processes which needs bash for their execution and bash itself is not utilizing any CPU time till now.

Usually when we use the command ps we add parameters like `-a`, `-u` and` -x`. While 

* `a` = show processes for all users
* `u` = display the process’s user/owner
* `x` = also show processes not attached to a terminal

```
root@ubuntu16-1:~# ps -aux | head -10
USER        PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root          1  0.0  0.3 185244  3856 ?        Ss   Oct11   0:07 /sbin/init auto noprompt
root          2  0.0  0.0      0     0 ?        S    Oct11   0:00 [kthreadd]
root          4  0.0  0.0      0     0 ?        I<   Oct11   0:00 [kworker/0:0H]
root          6  0.0  0.0      0     0 ?        I<   Oct11   0:00 [mm_percpu_wq]
root          7  0.0  0.0      0     0 ?        S    Oct11   0:12 [ksoftirqd/0]
root          8  0.0  0.0      0     0 ?        I    Oct11   0:19 [rcu_sched]
root          9  0.0  0.0      0     0 ?        I    Oct11   0:00 [rcu_bh]
root         10  0.0  0.0      0     0 ?        S    Oct11   0:00 [migration/0]
root         11  0.0  0.0      0     0 ?        S    Oct11   0:01 [watchdog/0]
```

where column are :

*   **USER –** Specifies the user who executed the program.

     **PID:** Process ID, shows the process identification number.

    **CPU%**: The processor % used by the process.

    **MEME%**: The memory % used by the process.

    **VSZ:** The virtual size in kbytes.

    **RSS:** In contrast with the  virtual size, this shows the real memory used by the process.

    **TTY:** Identifies the terminal from which the process was executed.

    **STATE:** Shows information on the process’ state just as it’s priority, by running “man ps” you can see codes meaning.

    **START:** Show when the process has started.

    **TIME:** Shows the processor’s time occupied by the program.

    **C0MMAND:** Shows the command used to launch the process.

> We can also use ps -ef instead of ps aux . There are no **differences** in the output because the meanings are the same. The **difference between ps** -**ef and ps aux** is due to historical divergences **between** POSIX and BSD systems. At the beginning, POSIX accepted the -**ef** while the BSD accepted only the **aux** form. Both list all processes of all users. In that aspect `-e` and `ax` are completely equivalent.

| Options for ps command | Description                                   |
| ---------------------- | --------------------------------------------- |
| ps -T                  | View Processes  associated with a terminal    |
| ps -x                  | View all processes owned by you               |
| ps -o  column_name     | view process according to user-defined format |

It is also possible to use --sort option to sort output based on different fields (+ for ascending & - for descending). `ps -eo  pid,ppid,cmd,%mem,%cpu --sort=-%mem | head -10` . With`-o` or `–format` options, ps allows us to build user-defined output formats.

|  **Process selection commands**   | Description                             |
| --------------------------------- | --------------------------------------- |
| ps -C command_name                | Select the process by the command name. |
| ps p process_id                   | View process by process ID.             |
| ps -u user_name/ID                | Select by user name or ID               |
| ps -g group_name , ps -G group_id | Select by group name or ID              |
| ps -t pst/0                       | Display Processes by TTY                |

We already know about the grep command in Linux, which searches for a pattern, and then prints the matching text in the output. What if the requirement is to apply this kind of processing to fetch select information about processes currently running in the system?

### pgrep

the `pgrep` command searches for processes currently running on the system, **based on a complete** or** partial process name**, or other specified attributes.

```
pgrep [options] pattern
```

```
root@ubuntu16-1:~# sleep 1000 &
[1] 60647
root@ubuntu16-1:~# sleep 2000 &
[2] 60648
root@ubuntu16-1:~# pgrep sleep
60647
60648
```

> Always use ps -ef command to make sure about process_name. There is different between process_name and the running program(like bash). compare pgrep -a and pgrep -af.

pgrep options:

```
-d, --delimiter <string>  specify output delimiter
 -l, --list-name           list PID and process name
 -a, --list-full           list PID and full command line
 -v, --inverse             negates the matching
 -w, --lightweight         list all TID
 -c, --count               count of matching processes
 -f, --full                use full process name to match
 -g, --pgroup <PGID,...>   match listed process group IDs
 -G, --group <GID,...>     match real group IDs
 -n, --newest              select most recently started
 -o, --oldest              select least recently started
 -P, --parent <PPID,...>   match only child processes of the given parent
 -s, --session <SID,...>   match session IDs
 -t, --terminal <tty,...>  match by controlling terminal
 -u, --euid <ID,...>       match by effective IDs
 -U, --uid <ID,...>        match by real IDs
 -x, --exact               match exactly with the command name
 -F, --pidfile <file>      read PIDs from file
 -L, --logpidfile          fail if PID file is not locked
 --ns <PID>                match the processes that belong to the same
                           namespace as <pid>
 --nslist <ns,...>         list which namespaces will be considered for
                           the --ns option.
                           Available namespaces: ipc, mnt, net, pid, user, uts

 -h, --help     display this help and exit
 -V, --version  output version information and exit
```

> **Real Time process monitoring ?**
>
> Be creative and use combination of  other commands like 'watch'. We can use 'watch' in conjunction with ps  command to perform Real-time Process Monitoring :
>
> `watch -n 1 'ps -eo pid,ppid,cmd,%mem,%cpu --sort=-%mem | head'`

But there is another tool for that, top.

###  top

 **top** command is used to show the Linux processes. It provides a dynamic real-time view of the running system. Usually, this command shows the summary information of the system and the list of processes or threads which are currently managed by the Linux Kernel.

```
root@ubuntu16-1:~# top
```

```
Tasks: 237 total,   1 running, 174 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.8 us,  0.4 sy,  0.0 ni, 98.7 id,  0.1 wa,  0.0 hi,  0.0 si,  0.0 st
KiB Mem :   985080 total,   167700 free,   501856 used,   315524 buff/cache
KiB Swap:  1045500 total,   448216 free,   597284 used.   256968 avail Mem 

   PID USER      PR  NI    VIRT    RES    SHR S %CPU %MEM     TIME+ COMMAND    
 59562 root      20   0   41952   3604   2920 R 12.5  0.4   0:00.02 top        
     1 root      20   0  185244   3856   2244 S  0.0  0.4   0:07.76 systemd    
     2 root      20   0       0      0      0 S  0.0  0.0   0:00.01 kthreadd   
     4 root       0 -20       0      0      0 I  0.0  0.0   0:00.00 kworker/0:+
     6 root       0 -20       0      0      0 I  0.0  0.0   0:00.00 mm_percpu_+
     7 root      20   0       0      0      0 S  0.0  0.0   0:12.88 ksoftirqd/0
     8 root      20   0       0      0      0 I  0.0  0.0   0:19.92 rcu_sched  
     9 root      20   0       0      0      0 I  0.0  0.0   0:00.00 rcu_bh     
    10 root      rt   0       0      0      0 S  0.0  0.0   0:00.00 migration/0
```

Top output keep refreshing until you press ‘q‘.

Where,

* **PID:** Shows task’s unique process id
* **USER:** User name of owner of task.
* **PR:** Stands for priority of the task.
* **NI:** Represents a Nice Value of task. A Negative nice value implies higher priority, and positive Nice value means lower priority.
* **VIRT:** Total virtual memory used by the task.
* **RES:**It is the Resident size, the non-swapped physical memory a task has used.
* **SHR:** Represents the amount of shared memory used by a task.
* **%CPU:** Represents the CPU usage.
* **%MEM:** Shows the Memory usage of task.
* **TIME+:** CPU Time, the same as ‘TIME’, but reflecting more granularity through hundredths of a second.
* **COMMAND:**Shows the command used to launch the process.

| top command option    | description                                |
| --------------------- | ------------------------------------------ |
| top -n 10             | Exit Top Command After Specific repetition |
| top -u user1          |  Display Specific User Process             |
| Top -d seconds.tenths | It tells delay time between screen updates |
| top -h                |  Shows top command syntax                  |

| Running top command hot keys | Description                                                  |
| ---------------------------- | ------------------------------------------------------------ |
|  pressing ‘d‘                | change screen refresh interval (default 3.0 sec)             |
| Pressing ‘z‘                 | display running process in color                             |
| Pressing ‘c‘                 | display absolute path of running program                     |
| pressing ‘k‘                 | kill a process after finding PID of process(without exiting) |
| Pressing 'M'                 | sort based on memory usage                                   |
| Shift+P                      |  Sort by CPU Utilisation                                     |
|  Press ‘h‘                   | Getting top command help                                     |

### Manage processes

To manage processes in a linux machine  we can send signals signals to the process.Many Signals are defined in the linux kernels. (try `man 7 signal`)

| Signal Name | Signal Number | Description                                                                                         |
| ----------- | ------------- | --------------------------------------------------------------------------------------------------- |
| SIGHUP      | 1             | Hang up detected on controlling terminal or death of controlling process                            |
| SIGINT      | 2             | Issued if the user sends an interrupt signal (Ctrl + C)                                             |
| SIGQUIT     | 3             | Issued if the user sends a quit signal (Ctrl + D)                                                   |
| SIGKILL     | 9             | If a process gets this signal it must quit immediately and will not perform any clean-up operations |
| SIGTERM     | 15            | Software termination signal (sent by kill by default)                                               |
| SIGCOUNT    | 18            | Continue the process stopped with STOP                                                              |
| STOP        | 19            | Stop process                                                                                        |

to send signals to processes there are some commands:

### kill

 `kill` command in Linux (located in /bin/kill), is a built-in command which is used to terminate processes manually . _kill_ command sends a signal to a process which terminates the process.

```
kill {-signal | -s signal} pid 
```

please notice that:

* A user can kill all his process.
* A user can not kill another user’s process.
* A user can not kill processes System is using.
* A root user can kill System-level-process and the process of any user.

note: If the user doesn’t specify any signal which is to be sent along with kill command then **default TERM signal** is sent that terminates the process.

```
root@ubuntu16-1:~# sleep 1000 &
[1] 61080
root@ubuntu16-1:~# sleep 2000 &
[2] 61081
root@ubuntu16-1:~# jobs -l
[1]- 61080 Running                 sleep 1000 &
[2]+ 61081 Running                 sleep 2000 &
root@ubuntu16-1:~# ps -ef | grep sleep
root      61080  57301  0 02:55 pts/18   00:00:00 sleep 1000
root      61081  57301  0 02:55 pts/18   00:00:00 sleep 2000
root      61085  57301  0 02:56 pts/18   00:00:00 grep --color=auto sleep
root@ubuntu16-1:~# kill 61080
root@ubuntu16-1:~# ps -ef | grep sleep
root      61081  57301  0 02:55 pts/18   00:00:00 sleep 2000
root      61089  57301  0 02:56 pts/18   00:00:00 grep --color=auto sleep
[1]-  Terminated              sleep 1000
root@ubuntu16-1:~# ps -ef | grep sleep
root      61081   1722  0 02:55 ?        00:00:00 sleep 2000
root      61096  55644  0 02:58 pts/17   00:00:00 grep --color=auto sleep
root@ubuntu16-1:~# kill -9 61081
root@ubuntu16-1:~# ps -ef | grep sleep
root      61100  55644  0 02:58 pts/17   00:00:00 grep --color=auto sleep
```

  use `kill -l` to see all signals you can send using kill.

{% hint style="danger" %}
There are two commands used to kill a process:

* kill – Kill a process by ID
* killall,pkill – Kill a process by name

killing a proccess by name could be realy dangerous, Before sending signal,  verify which process is matching the criteria using “pgrep -l”.
{% endhint %}

### killall

 `killall` is a tool for terminating running processes on your system **based on name**. _In contrast, `kill` terminates processes based on Process ID number (PID)_. Like `kill` , `killall` can also send specific system signals to processes.

```
kill {-signal | -s signal} process_name 
```

note1:the whole process_name should be defined ( ex : sleep not sle or slee).

note2: If no signal name is specified, SIGTERM is sent.

```
root@ubuntu16-1:~# sleep 1000 &
[1] 61836
root@ubuntu16-1:~# sleep 2000 &
[2] 61837
root@ubuntu16-1:~# ps -ef | grep sleep | grep -v grep
root      61836  55644  0 03:46 pts/17   00:00:00 sleep 1000
root      61837  55644  0 03:46 pts/17   00:00:00 sleep 2000
root@ubuntu16-1:~# pkill sleep
[1]-  Terminated              sleep 1000
[2]+  Terminated              sleep 2000
root@ubuntu16-1:~# ps -ef | grep sleep | grep -v grep
```

| killall command example    | Description                                                            |
| -------------------------- | ---------------------------------------------------------------------- |
| killall -l                 | all signals the killall command can send                               |
| killall -q process_name    | prevent killall from complaining if specified process doesn't exist    |
| killall -u \[user-name]    | kill all processes owned by a user                                     |
| killall -o 5h              | kill all processes that have now been running for more than _5_ hour   |
| killall -y 4h              | kill all precesses  that less than 4 hours old                         |
| killall -w \[process-name] |  causes `killall` to wait until the process terminates before exiting. |

### pkill

The PKill command allows you to kill a program simply **by specifying the name.**

```
pkill [options] pattern
```

note: We don't have to define whole process_name. So it could be really dangerous!

example:

```
root@ubuntu16-1:~# pgrep firefox
62256
root@ubuntu16-1:~# pkill firefox
```

| pkill command example     | Description                                      |
| ------------------------- | ------------------------------------------------ |
| pkill -c \[process_name]  | return a count of the number of processes killed |
| pkill -U \[real_user_ID]  | kill all the processes for a particular user     |
| pkill -G \[real_group_ID] | kill all the programs in a particular group      |

### free

 **`free`** command  displays the total amount of **free space** available along with the amount of **memory used** and **swap** memory in the system, and also the** buffers** used by the kernel.

```
free [options]
```

As free displays the details of the memory related to the system , its syntax doesn’t need any arguments to be passed but it has some options!

```
root@ubuntu16-1:~# free
              total        used        free      shared  buff/cache   available
Mem:         985080      432716      135464       16724      416900      339484
Swap:       1045500      671864      373636
```

free command with no options produces the columnar output as shown above where column:

1. **total **: displays the total installed memory _(MemTotal and SwapTotal i.e present in /proc/meminfo)._
2. **used :** displays the used memory.
3. **free :** displays the unused memory.
4. **shared :** displays the memory used by tmpfs_(Shmen i.epresent in /proc/meminfo and displays zero in case not available)._
5. **buffers :** displays the memory used by kernel buffers.
6. **cached :** displays the memory used by the page cache and slabs_(Cached and Slab available in /proc/meminfo)._
7. **buffers/cache :** displays the sum of buffers and cache.

 By default the display is in kilobytes, but you can override this using `-b` for bytes, `-k` for kilobytes, `-m` for megabytes, or `-g` for gigabytes.

```
root@ubuntu16-1:~# free -h
              total        used        free      shared  buff/cache   available
Mem:           961M        423M        128M         16M        410M        330M
Swap:          1.0G        655M        365M
```

 `-t`   displays an additional line containing the total of the total, used and free columns:

```
root@ubuntu16-1:~# free -t -h
              total        used        free      shared  buff/cache   available
Mem:           961M        424M        127M         16M        410M        329M
Swap:          1.0G        655M        365M
Total:         1.9G        1.1G        492M
```

Other free command options:

```
-h, --human         show human-readable output
     --si            use powers of 1000 not 1024
 -l, --lohi          show detailed low and high memory statistics
 -t, --total         show total for RAM + swap
 -s N, --seconds N   repeat printing every N seconds
 -c N, --count N     repeat printing N times, then exit
 -w, --wide          wide output

     --help     display this help and exit
 -V, --version  output version information and exit
```

### uptime

 The `uptime` command shows you a one-line display that includes the current time, how long the system has been running, how many users are currently logged on, and the **system load averages** for the past 1, 5, and 15 minutes.

```
uptime [-options]
```

```
root@ubuntu16-1:~# uptime 
 03:37:00 up 3 days, 19:07,  1 user,  load average: 0.00, 0.00, 0.00
```

Lets try` uptime -h` to see all of `uptime` availbale options:

```
Usage:
 uptime [options]

Options:
 -p, --pretty   show uptime in pretty format
 -h, --help     display this help and exit
 -s, --since    system up since
 -V, --version  output version information and exit

For more details see uptime(1).
```

examples:

```
root@ubuntu16-1:~# uptime -p
up 3 days, 19 hours, 16 minutes
root@ubuntu16-1:~# uptime -s
2019-10-17 08:29:08
root@ubuntu16-1:~# uptime -V
uptime from procps-ng 3.3.10
```

.

.

.

{% hint style="info" %}
Processes deep dive ( Beyond the scope of LPIC1)

 **Types of Processes**

1. **Parent and Child process :** The 2nd and 3rd column of the ps –f command shows process id and parent’s process id number. For each user process there’s a parent process in the system, with most of the commands having shell as their parent.
2. **Zombie and Orphan process :** After completing its execution a child process is terminated or killed and SIGCHLD updates the parent process about the termination and thus can continue the task assigned to it. But at times when the parent process is killed before the termination of the child process, the child processes becomes orphan processes, with the parent of all processes “init” process, becomes their new ppid.\
    A process which is killed but still shows its entry in the process status or the process table is called a zombie process, they are dead and are not used.
3. **Daemon process :** They are system-related background processes that often run with the permissions of root and services requests from other processes, they most of the time run in the background and wait for processes it can work along with for ex print daemon.\
    When ps –ef is executed, the process with ? in the tty field are daemon processes

**States of a Process in Linux**

* **Running** – here it’s either running (it is the current process in the system) or it’s ready to run (it’s waiting to be assigned to one of the CPUs). use ps -r command.
* **Waiting** – in this state, a process is waiting for an event to occur or for a system resource. Additionally, the kernel also differentiates between two types of waiting processes; interruptible waiting processes – can be interrupted by signals and uninterruptible waiting processes – are waiting directly on hardware conditions and cannot be interrupted by any event/signal.
* **Stopped** – in this state, a process has been stopped, usually by receiving a signal. For instance, a process that is being debugged.
* **Zombie** – here, a process is dead, it has been halted but it’s still has an entry in the process table.

**Processes state codes in ps aux or ps -ef  command:**

* `R` running or runnable (on run queue)
* `D` uninterruptible sleep (usually IO)
* `S` interruptible sleep (waiting for an event to complete)
* `Z` defunct/zombie, terminated but not reaped by its parent
* `T` stopped, either by a job control signal or because it is being traced

Some extra modifiers:

* `<` high-priority (not nice to other users)
* `N` low-priority (nice to other users)
* `L` has pages locked into memory (for real-time and custom IO)
* `s` is a session leader
* `l` is multi-threaded (using CLONE_THREAD, like NPTL pthreads do)
* `+` is in the foreground process group
{% endhint %}

.

[https://www.thegeekdiary.com/understanding-the-job-control-commands-in-linux-bg-fg-and-ctrlz/](https://www.thegeekdiary.com/understanding-the-job-control-commands-in-linux-bg-fg-and-ctrlz/)

[https://superuser.com/questions/662431/what-exactly-determines-if-a-backgrounded-job-is-killed-when-the-shell-is-exited](https://superuser.com/questions/662431/what-exactly-determines-if-a-backgrounded-job-is-killed-when-the-shell-is-exited)

[https://linuxhint.com/nohup_command_linux/](https://linuxhint.com/nohup_command_linux/)

[https://www.tecmint.com/run-linux-command-process-in-background-detach-process/](https://www.tecmint.com/run-linux-command-process-in-background-detach-process/)

[https://www.geeksforgeeks.org/screen-command-in-linux-with-examples/](https://www.geeksforgeeks.org/screen-command-in-linux-with-examples/)

[https://www.tecmint.com/screen-command-examples-to-manage-linux-terminals/](https://www.tecmint.com/screen-command-examples-to-manage-linux-terminals/)

[https://linoxide.com/linux-command/15-examples-screen-command-linux-terminal/](https://linoxide.com/linux-command/15-examples-screen-command-linux-terminal/)

[https://ryanstutorials.net/linuxtutorial/processes.php](https://ryanstutorials.net/linuxtutorial/processes.php)

[https://www.cyberciti.biz/faq/unix-linux-disown-command-examples-usage-syntax/](https://www.cyberciti.biz/faq/unix-linux-disown-command-examples-usage-syntax/)

[https://linuxize.com/post/ps-command-in-linux/](https://linuxize.com/post/ps-command-in-linux/)

[https://www.computerhope.com/jargon/p/pid.htm](https://www.computerhope.com/jargon/p/pid.htm)

[https://www.tecmint.com/linux-process-management/](https://www.tecmint.com/linux-process-management/)

[https://www.geeksforgeeks.org/processes-in-linuxunix/](https://www.geeksforgeeks.org/processes-in-linuxunix/)

[https://www.geeksforgeeks.org/ps-command-in-linux-with-examples/](https://www.geeksforgeeks.org/ps-command-in-linux-with-examples/)

****[https://linuxhint.com/ps_command_linux-2/](https://linuxhint.com/ps_command_linux-2/)

[https://www.quora.com/What-is-the-difference-between-ps-elf-and-ps-aux-in-Linux](https://www.quora.com/What-is-the-difference-between-ps-elf-and-ps-aux-in-Linux)

[https://www.geeksforgeeks.org/top-command-in-linux-with-examples/](https://www.geeksforgeeks.org/top-command-in-linux-with-examples/)

[https://www.tecmint.com/12-top-command-examples-in-linux/](https://www.tecmint.com/12-top-command-examples-in-linux/)

[https://www.tutorialspoint.com/unix/unix-signals-traps.htm](https://www.tutorialspoint.com/unix/unix-signals-traps.htm)

[https://www.geeksforgeeks.org/kill-command-in-linux-with-examples/](https://www.geeksforgeeks.org/kill-command-in-linux-with-examples/)

[https://www.linux.com/tutorials/how-kill-process-command-line/](https://www.linux.com/tutorials/how-kill-process-command-line/)

[https://www.linode.com/docs/tools-reference/tools/use-killall-and-kill-to-stop-processes-on-linux/](https://www.linode.com/docs/tools-reference/tools/use-killall-and-kill-to-stop-processes-on-linux/)

[https://www.geeksforgeeks.org/free-command-linux-examples/](https://www.geeksforgeeks.org/free-command-linux-examples/)

[https://www.geeksforgeeks.org/linux-uptime-command-with-examples/](https://www.geeksforgeeks.org/linux-uptime-command-with-examples/)

[https://linuxhint.com/load_average_linux/](https://linuxhint.com/load_average_linux/)

.
