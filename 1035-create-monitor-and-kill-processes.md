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

```text
root@ubuntu16-1:~# ls
client.conf  files-backup            openvpn.key  unzip_6.0-20ubuntu1_amd64.deb
dead.letter  jcal_0.4.1-2_amd64.deb  test-space   zip_3.0-11_amd64.deb
```

A job might be executed and exit immediately and runs for a short time only but some jobs run for a long time.

```text
root@ubuntu16-1:~# sleep 3600

```

that goes to sleep for 3600 seconds, that is an hour! So you can wait for an hour \(considering that you can not use your terminal\) or you can run a job in background.

### Foreground and Background jobs

 A job is a process that the shell manages. Each job is assigned a sequential job ID. Because a job is a process, each job has an associated PID. There are two types of job statuses:

1. **Foreground**: When we enter a command in a terminal window, the command occupies that terminal window until it completes. This is a foreground job.  
 2. **Background**: When we enter an ampersand \(&\) symbol at the end of a command line, the command runs without occupying the terminal window. The shell prompt is displayed immediately after you press Return. This is an example of a background job.

```text
root@ubuntu16-1:~# sleep 1000 &
[1] 54786
root@ubuntu16-1:~#
```

**Job Control Commands**

Job control commands enable **us** to place jobs in the foreground or background, and to start or stop jobs. The table describes the job control commands.

| Command | Description |
| :--- | :--- |
| jobs | Lists all jobs |
| bg %n | Places the current or specified job in the background, where n is the job ID |
| fg %n | Brings the current or specified job into the foreground, where n is the job ID |
| Control-Z | Stops the foreground job and places it in the background as a stopped job |

> if no job id is mentioned bg and fg consider the most recent job

### jobs

```text
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

```text
root@ubuntu16-1:~# bg
[3]+ sleep 3000 &
root@ubuntu16-1:~# jobs
[1]   Running                 sleep 1000 &
[2]-  Running                 sleep 2000 &
[3]+  Running                 sleep 3000 &
```

we cloud use `fg %3` command and it would have the same result. 

### fg

```text
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

jobs normally stick to the shell running it, so they are killed when we close the terminal or log out. Some times we need to make sure that the running job is not attached to the running shell.

### nohup

`Nohup` stands for **no hangup**, and that means even if the parent shell is diconnected the job just will continue,  The output of the **nohup** command will write in **nohup.out** the file if any redirecting filename is not mentioned in **nohup** command.

We can use the nohup command by two ways.

1. `nohup command [args…]`
2. `nohup option`

Using nohup with commands:

```text
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

```text
root@ubuntu16-1:~/test-space# jobs
root@ubuntu16-1:~/test-space# ps aux | grep ping | grep -v grep
root      55210  0.0  0.0   6536   756 ?        S    02:32   0:00 ping 8.8.8.8
root@ubuntu16-1:~/test-space# 
```

> Please notice that we can not use `fg`, `bg` commands on that particular job anymore.

 When we run nohup command without ‘**&’** then it returns to shell command prompt immediately after running that particular command in the background.

> We useually use nohup with output redirection `nohup bash script.sh > myresult.txt`

| nohup option | Description |
| :--- | :--- |
| --help |  display this help and exit |
| --version |  output version information and exit |

> **disown**
>
> We can also use **disown** command, it is used after the a process has been launched and put in the background, it’s work is to remove a shell job from the shell’s active list jobs, therefore we can not use `fg`, `bg` commands on that particular job anymore.
>
> `root@ubuntu16-1:~#sleep 666 &`
>
> `root@ubuntu16-1:~# sleep 666 & [1] 55458` 
>
> `root@ubuntu16-1:~# jobs [1]+ Running sleep 666 &` 
>
> `root@ubuntu16-1:~# disown %1 root@ubuntu16-1:~# jobs`

There a problem with nohup and disown commands. There is no way to bring back that job to forground and work interactivly with that. So we need a different solution, screen.

### screen

 **screen** command in Linux provides the ability to launch and use multiple shell sessions from a single _ssh_ session. When a process is started with ‘screen’, the process can be detached from session & then can reattach the session at a later time. When the session is detached, the process that was originally started from the screen is still running and managed by the screen itself. The process can then re-attach the session at a later time, and the terminals are still there, the way it was left. \(you might need to install it\)

#### Start screen for the first time

simple use screen command  **:**

```text
root@ubuntu16-1:~# screen
```

```text
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

use `screen -s Session_Name` to start a named session.now lets run a command inside screen:

```text
root@ubuntu16-1:~# sleep 1111

```

> Inorder to create a new screen inside the current screen \(nested screen\) use  just press **Ctrl-a** +**c**

#### Detach the screen

One of the advantages of screen that is you can detach it. Then, you can restore it without losing anything you have done on the screen. use  **Ctrl-a + d** to detach**:**

```text
root@ubuntu16-1:~# sleep 1111

```

```text
root@ubuntu16-1:~# screen
[detached from 56798.pts-4.ubuntu16-1]
root@ubuntu16-1:~# 
```

 **-d** is **also** used to detach a screen session so that it can be reattached in future.

**List screens**

 **screen  -ls** is used to display the currently opened screens including those running in the background:

```text
root@ubuntu16-1:~# screen -list
There is a screen on:
	56798.pts-4.ubuntu16-1	(10/14/2019 04:15:00 AM)	(Detached)
1 Socket in /var/run/screen/S-root.
```

**Reattach to a screen**

 **-r**  It is used to reattach a screen session which was detached in past:

```text
root@ubuntu16-1:~# screen -r 56798
```

```text
root@ubuntu16-1:~# sleep 1111
root@ubuntu16-1:~# 

```

> We usually use `screen -dr <Screen-ID>` command.This means detach the specified screen first and then reattach it.

#### Switching between screens

When we do nested screen, you can switch between screen using command **Ctrl-a +n**. It will be move to the next screen. When  need to go to the previous screen, just press **Ctrl-a** +**p**.

#### Terminate screen session

Use  “**Ctrl-A**” and “**K**” to kill the screen.



### Signal a program to continue running after logout

#### What is process?

A program is a series of instructions that tell the computer what to do. When we run a program, those instructions are copied into memory and space is allocated for variables and other stuff required to manage its execution. This running instance of a program is called a process and it's processes which we manage

#### Manage processes

To manage processes in a linux machine  we can send signals signals to the process.Many Signals are defined in the linux kernels. \(try `man 7 signal`\)

| Signal Name | Signal Number | Description |
| :--- | :--- | :--- |
| SIGHUP | 1 | Hang up detected on controlling terminal or death of controlling process |
| SIGINT | 2 | Issued if the user sends an interrupt signal \(Ctrl + C\) |
| SIGQUIT | 3 | Issued if the user sends a quit signal \(Ctrl + D\) |
| SIGFPE | 8 | Issued if an illegal mathematical operation is attempted |
| SIGKILL | 9 | If a process gets this signal it must quit immediately and will not perform any clean-up operations |
| SIGALRM | 14 | Alarm clock signal \(used for timers\) |
| SIGTERM | 15 | Software termination signal \(sent by kill by default\) |



to send signals to processes there are some commands.





> If we use the `jobs` command with the `-p` option, the output is simply the PID of the _process group leader_ for each job.



\*\*\*\*

\*\*\*\*



In linux if we want to run some thing in background we put & at end of it.

.

.

.

[https://www.thegeekdiary.com/understanding-the-job-control-commands-in-linux-bg-fg-and-ctrlz/](https://www.thegeekdiary.com/understanding-the-job-control-commands-in-linux-bg-fg-and-ctrlz/)

[https://linuxhint.com/nohup\_command\_linux/](https://linuxhint.com/nohup_command_linux/)

[https://www.tecmint.com/run-linux-command-process-in-background-detach-process/](https://www.tecmint.com/run-linux-command-process-in-background-detach-process/)

[https://www.geeksforgeeks.org/screen-command-in-linux-with-examples/](https://www.geeksforgeeks.org/screen-command-in-linux-with-examples/)

[https://www.tecmint.com/screen-command-examples-to-manage-linux-terminals/](https://www.tecmint.com/screen-command-examples-to-manage-linux-terminals/)

[https://linoxide.com/linux-command/15-examples-screen-command-linux-terminal/](https://linoxide.com/linux-command/15-examples-screen-command-linux-terminal/)

[https://ryanstutorials.net/linuxtutorial/processes.php](https://ryanstutorials.net/linuxtutorial/processes.php)

[https://www.tutorialspoint.com/unix/unix-signals-traps.htm](https://www.tutorialspoint.com/unix/unix-signals-traps.htm)

.

