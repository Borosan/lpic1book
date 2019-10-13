# 103.4. Use streams, pipes and redirects

**Weight:** 4

**Description:** Candidates should be able to redirect streams and connect them in order to efficiently process textual data. Tasks include redirecting standard input, standard output and standard error, piping the output of one command to the input of another command, using the output of one command as arguments to another command and sending output to both stdout and a file.

**Key Knowledge Areas:**

* Redirecting standard input, standard output and standard error
* Pipe the output of one command to the input of another command
* Use the output of one command as arguments to another command
* Send output to both stdout and a file

**Terms and Utilities:**

* tee
* xargs

We have talked about basics of piping and redirecting in previous sections and in this tutorial we take a closer look at Linux techniques for redirecting standard IO streams.

### Input, output, and streams <a id="input-output-and-streams"></a>

In computing, a stream is something that can transfer data. Data streams, like water streams, have two ends. They have a source and an outflow. 

A Linux shell, such as Bash, receives input and sends output as sequences or streams of characters. Stream of characters comes from or goes to a file, a keyboard, a window on a display, or some other IO device. 

stdin, stdout, and stederr are three standard streams that are established when a Linux command is executed. 

![](.gitbook/assets/usestreams-streams.jpg)

Linux shells use these three standard I/O **streams ,** each of which is associated with **file descriptor**:**:**

* **stdout** is the standard output stream, which displays output from commands\(**file descriptor** **1**\)
* **stderr** is the standard error stream, which displays error output from commands\(**file descriptor** **2**\)
* **stdin** is the standard input stream, which provides input to commands\(**file** **descriptor** **0**\)

 So  we can see that there are two output streams, `stdout` and `stderr`, and one input stream, `stdin`

**What are file descriptors?**

Streams Are Handled Like Files.Streams in Linux—like almost everything else—are treated as though they were files. We can read text from a file, and we can write text into a file. Both of these actions involve a stream of data. So the concept of handling a stream of data as a file isn’t that much of a stretch.

Each file associated with a process is allocated a unique number to identify it. This is known as the **file descriptor**. Whenever an action is required to be performed on a file, the file descriptor is used to identify the file.  These values are always used for `stdin`, `stdout,` and `stderr`:

* 0: stdin 
* 1: stdout 
* 2: stderr

```text
 root@ubuntu16-1:~/test-space/myfiles# ls -al /dev/std*
lrwxrwxrwx 1 root root 15 Dec  1  2018 /dev/stderr -> /proc/self/fd/2
lrwxrwxrwx 1 root root 15 Dec  1  2018 /dev/stdin -> /proc/self/fd/0
lrwxrwxrwx 1 root root 15 Dec  1  2018 /dev/stdout -> /proc/self/fd/1
```

### Redirecting standard IO <a id="redirecting-standard-io"></a>

 Although the model for standard input and output is a serial stream of characters to and from a terminal, we might want to prepare input data in a file, or save output or error information in a file. That’s where _redirection_ comes in.

#### Redirecting output <a id="redirecting-output"></a>

There are two ways to redirect output to a file:

_**n**_**&gt;**  redirects output from file descriptor n to a file. You must have write authority to the file. If the file does not exist, it is created. If it does exist, the existing contents are usually lost without any warning.

_**n**_**&gt;&gt;**  also redirects output from file descriptor n to a file. Again, we must have write authority to the file. If the file does not exist, it is created. If it does exist, the output is appended to the existing file.

> The _n_ in n&gt; or n&gt;&gt; refers to the _file descriptor_. If it omitted, then standard output \(file descriptor 1\) is assumed.

The `>` and `>>` redirection symbols works with **stdout** by default. We can use one of the numeric file descriptors to indicate which standard output stream you wish to redirect.

* **&gt;** - standard output
* **2&gt;** - standard error

"&gt;" redirects stdout

```text
root@ubuntu16-1:~/test-space/myfiles# ls -l
total 1144
drwxr-xr-x 2 root root    4096 Sep 30 05:42 dir1
drwxr-xr-x 2 root root    4096 Oct  5 05:25 dir2
-rw-r--r-- 1 root root    2084 Sep 30 05:44 myconf.txt
-rw-r----- 1 root root 1156369 Sep 30 05:41 mylog.txt
root@ubuntu16-1:~/test-space/myfiles# ls -l > list
root@ubuntu16-1:~/test-space/myfiles# cat list 
total 1144
drwxr-xr-x 2 root root    4096 Sep 30 05:42 dir1
drwxr-xr-x 2 root root    4096 Oct  5 05:25 dir2
-rw-r--r-- 1 root root       0 Oct  9 01:42 list
-rw-r--r-- 1 root root    2084 Sep 30 05:44 myconf.txt
-rw-r----- 1 root root 1156369 Sep 30 05:41 mylog.txt
```

"2&gt;" redirects stderr

```text
root@ubuntu16-1:~/test-space/myfiles# ls -l BlahBlah
ls: cannot access 'BlahBlah': No such file or directory
root@ubuntu16-1:~/test-space/myfiles# ls -l BlahBlah 2> error
root@ubuntu16-1:~/test-space/myfiles# ls -l
total 1152
drwxr-xr-x 2 root root    4096 Sep 30 05:42 dir1
drwxr-xr-x 2 root root    4096 Oct  5 05:25 dir2
-rw-r--r-- 1 root root      56 Oct  9 01:43 error
-rw-r--r-- 1 root root     267 Oct  9 01:42 list
-rw-r--r-- 1 root root    2084 Sep 30 05:44 myconf.txt
-rw-r----- 1 root root 1156369 Sep 30 05:41 mylog.txt
root@ubuntu16-1:~/test-space/myfiles# cat error 
ls: cannot access 'BlahBlah': No such file or directory
```

**Append**

 Commands with a double bracket _do not_ overwrite the destination’s existing contents.

* **&gt;&gt;** - standard output
* **2&gt;&gt;** - standard error

```text
root@ubuntu16-1:~/test-space/myfiles# date
Sat Oct 12 02:52:04 PDT 2019
root@ubuntu16-1:~/test-space/myfiles# date > mydate
root@ubuntu16-1:~/test-space/myfiles# cat mydate
Sat Oct 12 02:52:10 PDT 2019
root@ubuntu16-1:~/test-space/myfiles# date > mydate 
root@ubuntu16-1:~/test-space/myfiles# cat mydate 
Sat Oct 12 02:53:00 PDT 2019
root@ubuntu16-1:~/test-space/myfiles# date >> mydate 
root@ubuntu16-1:~/test-space/myfiles# cat mydate 
Sat Oct 12 02:53:00 PDT 2019
Sat Oct 12 02:53:23 PDT 2019
```

{% hint style="info" %}
 We said that output redirection using n&gt; usually overwrites existing files. You can control this with the `noclobber` option of the `set` builtin. If it has been set, we can override it using n&gt;\|
{% endhint %}

**redirect both standard output and standard error**

Sometimes, we might want to redirect both standard output and standard error into a file. This is often done for automated processes or background jobs so that we can review the output later.

* Use **&&gt;** or **&&gt;&gt;** to redirect both standard output and standard error to the same place**.**

```text
root@ubuntu16-1:~/test-space/myfiles# ls blah &>result
root@ubuntu16-1:~/test-space/myfiles# cat result 
ls: cannot access 'blah': No such file or directory
root@ubuntu16-1:~/test-space/myfiles# ls blah2 &>>result
root@ubuntu16-1:~/test-space/myfiles# cat result 
ls: cannot access 'blah': No such file or directory
ls: cannot access 'blah2': No such file or directory
```

We can do it in another way,  redirect file descriptor _n_ and then redirect file descriptor _m_ to the same place using the construct m&gt;&n or m&gt;&gt;&n.

* **&1** and **&2** and **&0**  reffer to current place of **stdout** , **stderror** and **stdin**.

```text
root@ubuntu16-1:~/test-space/myfiles# ls blah3 > result 2>&1
root@ubuntu16-1:~/test-space/myfiles# cat result 
ls: cannot access 'blah3': No such file or directory
```

{% hint style="info" %}
 The order in which outputs are redirected is important. For example,  
`command 2>&1 >output.txt`  
 is not the same as  
`command >output.txt 2>&1`

In the first case, stderr is redirected to the current location of stdout and then stdout is redirected to output.txt, but this second redirection affects only stdout, not stderr. In the second case, stderr is redirected to the current location of stdout and that is output.txt.
{% endhint %}

 **redirecting to /dev/null**

At other times, we ****might want to ignore either standard output or standard error entirely. To do this, redirect the appropriate stream to the empty file, /dev/null.

/dev/null is a special file called the null device. it is also called the bit-bucket or the blackhole because it immediately discards anything written to it and only returns an end-of-file EOF when read.

```text
root@ubuntu16-1:~/test-space/myfiles# ls -l
total 1156
drwxr-xr-x 2 root root    4096 Sep 30 05:42 dir1
drwxr-xr-x 2 root root    4096 Oct  5 05:25 dir2
-rw-r--r-- 1 root root     170 Oct  9 02:25 error
-rw-r--r-- 1 root root      97 Oct  9 03:17 list
-rw-r--r-- 1 root root    2084 Sep 30 05:44 myconf.txt
-rw-r--r-- 1 root root      58 Oct 12 02:53 mydate
-rw-r----- 1 root root 1156369 Sep 30 05:41 mylog.txt
root@ubuntu16-1:~/test-space/myfiles# ls -l Blah 2>/dev/null
```

### redirecting input

"&lt;" redirects stdin 

```text
root@ubuntu16-1:~/test-space/myfiles# cat list 
total 1144
drwxr-xr-x 2 root root    4096 Sep 30 05:42 dir1
drwxr-xr-x 2 root root    4096 Oct  5 05:25 dir2
-rw-r--r-- 1 root root       0 Oct  9 01:42 list
-rw-r--r-- 1 root root    2084 Sep 30 05:44 myconf.txt
-rw-r----- 1 root root 1156369 Sep 30 05:41 mylog.txt
root@ubuntu16-1:~/test-space/myfiles# wc < list 
  6  47 267
```

or simply wc 0&lt; list do the same thing.

### here documents

A here document \(or heredoc\) is a way of getting text input into a script or command without having to feed it in from a separate file.

```text
Command << HeredocDelimiter
. . .
. . .
. . .
HeredocDelimiter
```

**&lt;&lt;**  redirects standard input and the command will take input until "HeredocDelimiter" string .

 **Note:** There should not be any space between the &lt;&lt; symbol and the limit string. If there are any characters between the limit string and the &lt;&lt; symbol then the here document is unlikely to work.

```text
root@ubuntu16-1:~/test-space/myfiles# cat << EOF > /tmp/myfile.txt
> Theis line will be written to the file.
>     this line is 2nd line
> this is last line.
> EOF
root@ubuntu16-1:~/test-space/myfiles# cat /tmp/myfile.txt 
Theis line will be written to the file.
    this line is 2nd line
this is last line.
```

> &lt;&lt;-   HereDoc uses **‘–‘** symbol to suppress any tab space from each line of heredoc text.

> "-" A hyphen \(used alone\) generally signifies that input will be taken from `stdin` as opposed to a named file. try: cat - &lt;&lt; EOF &gt; interesting.txt

### here string

 **&lt;&lt;&lt;** Redirect a single line of text to the stdin ofcmd. This is called a here-string.

```text
cmd <<< "string"
```

### **Pipes**

We use the `|` \(pipe\) operator between two commands to direct the stdout of the first to the stdin of the second. **`command | command`**

```text
root@ubuntu16-1:~/test-space/myfiles# ls -l
total 1156
drwxr-xr-x 2 root root    4096 Sep 30 05:42 dir1
drwxr-xr-x 2 root root    4096 Oct  5 05:25 dir2
-rw-r--r-- 1 root root     170 Oct  9 02:25 error
-rw-r--r-- 1 root root      97 Oct  9 03:17 list
-rw-r--r-- 1 root root    2084 Sep 30 05:44 myconf.txt
-rw-r--r-- 1 root root      58 Oct 12 02:53 mydate
-rw-r----- 1 root root 1156369 Sep 30 05:41 mylog.txt
root@ubuntu16-1:~/test-space/myfiles# ls -l | cut -f1 -d' '
total
drwxr-xr-x
drwxr-xr-x
-rw-r--r--
-rw-r--r--
-rw-r--r--
-rw-r--r--
-rw-r-----
root@ubuntu16-1:~/test-space/myfiles# ls -l | cut -f1 -d' ' | wc -l
8
```

Some commands like grep can accept input as parameters, but some commands accepts arguments

> Parameter vs Argument: 
>
> ```text
>                  Parameters
>                    |     |
> function test ( param1 , param2 ){
>     return ( param1 + param2 );
>     }
> test (5, 6)
>       |  |
>      Arguments
> ```

### Using output as arguments <a id="using-output-as-arguments"></a>

In the privious of pipelines, we learned how to take the output of one command and use it as input to another command. Instead , what if  we want to use the output of a command or the contents of a file as arguments to a command rather than as input. Pipelines don’t work for that.

 Three common methods are:

1. The `xargs` command
2. The `find` command with the `-exec` option \(previous section\)
3. Command substitution

### xargs

**xargs** is a Unix command which can be used to build and execute commands from standard input.

```text
xargs [options] [command]
```

> If no command is specified, xargs executes echo by default.

```text
root@ubuntu16-1:~/test-space/myfiles# ls | xargs 
dir1 dir2 interesting.txt list myconf.txt mydate mylog.txt
```

 There are several ways in which **xargs** is useful in daily usage of the command line.

![](.gitbook/assets/usestreams-xarg.jpg)

```text
root@ubuntu16-1:~/test-space/myfiles# find . -type f | xargs -I FILE  echo this is my file FILE
this is my file ./dir1/myvideo.mp4
this is my file ./interesting.txt
this is my file ./list
this is my file ./dir2/zabbix-cli-rpm-2.1.1-1.tar.gz
this is my file ./dir2/zabbix-cli-manual-1.7.0.pdf
this is my file ./mylog.txt
this is my file ./myconf.txt
this is my file ./mydate
```

```text
xargs options :
-0 : input items are terminated by null character instead of white spaces
-a file : read items from file instead of standard input
–delimiter = delim : input items are terminated by a special character
-E eof-str : set the end of file string to eof-str
-I replace-str : replace occurrences of replace-str in the initial arguments with names read from standard input
-L max-lines : use at-most max-lines non-blank input lines per command line.
-p : prompt the user about whether to run each command line and read a line from terminal.
-r : If the standard input does not contain any nonblanks, do not run the command
-x : exit if the size is exceeded.
–help : print the summary of options to xargs and exit
–version : print the version no. of xargs and exit
```

| xarg command example | Description |
| :--- | :--- |
| xargs --version | Prints the version number of xargs command |
| xargs -a test.txt | It will show contents of file |
| xargs -p -a test.txt | -p option prompts for confirmation before running each command line. |
| xargs -r -a test.txt | -r option ensures if standard input is empty, then command is not executed |

### tee

 Sometimes we might want to see output on the screen while saving a copy for later. While we **could** do this by redirecting the command output to a file in one window and then using `tail -f` to follow the output in another screen, using the `tee` command is easier.

```text
tee [OPTION]... [FILE]...
```

 **tee** reads the standard input and writes it to both the standard output and one or more files.

![](.gitbook/assets/upstreams-tee.jpg)

tee does both the tasks simultaneously.example:

```text
root@ubuntu16-1:~/test-space/myfiles# ls  | tee list.txt
dir1
dir2
interesting.txt
list
list.txt
myconf.txt
mydate
mylog.txt
root@ubuntu16-1:~/test-space/myfiles# cat list.txt 
dir1
dir2
interesting.txt
list
list.txt
myconf.txt
mydate
mylog.txt
```

with  **`-a`**  option It basically do not overwrite the file but append to the given file.



.

.

.

[https://developer.ibm.com/tutorials/l-lpic1-103-4/](https://developer.ibm.com/tutorials/l-lpic1-103-4/)

[https://www.gnu.org/software/libc/manual/html\_node/Streams-and-File-Descriptors.html](https://www.gnu.org/software/libc/manual/html_node/Streams-and-File-Descriptors.html)

[https://geek-university.com/linux/streams/](https://geek-university.com/linux/streams/)

[https://www.howtogeek.com/435903/what-are-stdin-stdout-and-stderr-on-linux/](https://www.howtogeek.com/435903/what-are-stdin-stdout-and-stderr-on-linux/)

[https://www.digitalocean.com/community/tutorials/an-introduction-to-linux-i-o-redirection](https://www.digitalocean.com/community/tutorials/an-introduction-to-linux-i-o-redirection)

[https://www.serverwatch.com/columns/article.php/3860446/Shell-Scripts-and-Here-Documents.htm](https://www.serverwatch.com/columns/article.php/3860446/Shell-Scripts-and-Here-Documents.htm)

[https://linuxhint.com/bash-heredoc-tutorial/](https://linuxhint.com/bash-heredoc-tutorial/)

[https://stackoverflow.com/questions/40230008/whats-the-usages-of-hyphen-in-linux-shell](https://stackoverflow.com/questions/40230008/whats-the-usages-of-hyphen-in-linux-shell)

[https://www.geeksforgeeks.org/xargs-command-unix/](https://www.geeksforgeeks.org/xargs-command-unix/)

[https://www.tecmint.com/xargs-command-examples/](https://www.tecmint.com/xargs-command-examples/)

.

