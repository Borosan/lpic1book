# 105.2. Customize or write simple scripts

**Weight: **4

**Description: **Candidates should be able to customize existing scripts, or write simple new Bash scripts.

**Key Knowledge Areas:**

* Use standard sh syntax (loops, tests)
* Use command substitution
* Test return values for success or failure or other information provided by a command
* Perform conditional mailing to the superuser
* Correctly select the script interpreter through the shebang (#!) line
* Manage the location, ownership, execution and suid-rights of scripts

**Terms and Utilities:**

* for
* while
* test
* if
* read
* seq
* exec

 This  lesson is all about shell scripting in linux and we start writing some simple scripts. We can put almost every command we use, inside a shell script. But before starting there are some notes to consider.

### shebang (#!)

In Linux and opensource world script files are very important. There are different type of scripting language used to write script files. As file extension is just a label for a script,  **shebang** (file script file interpreter line )is used to specify the scripting language. 

```
#!/bin/bash

echo "Hello World!
```

Beside that, there are different kinds of shell and You can’t guarantee which shell your potential users might prefer.  shebang   makes scripts portable to all shell environments by  instructing  the shell to run your script in a particular shell.

> `#` and  `!` makes this line special because `#` is used as comment line in bash.

The shebang line must be the first line of your script, and the rest of the line contains the path to the shell that your program must run under.

### variables

In prior sections in this series, we  learned how to define a variable  `VARIABLE="value" `.  We can do the same thing in a script, as an example:

```
#!/bin/bash

MYNAME="payam"
echo "Hello $MYNAME!"
```

We have  assigned string values to variables. Bash supports shell arithmetic using integers. 

{% hint style="info" %}
**declare**

The **declare** is a builtin command of the **bash** shell. It is used to declare shell variables and functions, set their attributes and display their values.`  -i  `make a VARIABLE to have 'integer' attribute.

```
user1@ubuntu16-1:~/sandbox$ declare VAR1="hello"
user1@ubuntu16-1:~/sandbox$ declare -i VAR2="42"
```

**`-p` **displays the options and attributes of each variable name if it is used with name arguments:

```
user1@ubuntu16-1:~/sandbox$ declare -p VAR1 VAR2
declare -- VAR1="hello"
declare -i VAR2="42"
```
{% endhint %}

### command substitution

Sometimes we might need to use the result of a command for another command or a variable. 

If we  surround a command with `$(` and `)`, or with a pair of backticks,  we can substitute the command’s output as input to another command. This technique is called _command substitution_. 

```
$(command)
### OR
`command`
```

example:

```
user1@ubuntu16-1:~/sandbox$ MYLIST=$(ls -l)
user1@ubuntu16-1:~/sandbox$ echo $MYLIST
total 4 -rw-rw-r-- 1 user1 user1 58 فوریه 4 12:24 script1

user1@ubuntu16-1:~/sandbox$ MYSPACE=`du -sh`
user1@ubuntu16-1:~/sandbox$ echo $MYSPACE
8.0K .
```

### executing scripts

Inorder to run a shell script, the script should be  executabe.

```
user1@ubuntu16-1:~/sandbox$ ls -l
total 4
-rw-rw-r-- 1 user1 user1 58 فوریه  4 12:24 script1
user1@ubuntu16-1:~/sandbox$ chmod +x script1 
user1@ubuntu16-1:~/sandbox$ ls -l
total 4
-rwxrwxr-x 1 user1 user1 32 فوریه  4 14:14 script1
```

> Another way of executing a script is giving our script name as a parammeter to `sh` or `bash` commands. `sh ./script1` or `bash ./script1`

### locating scripts

if our script is executable we can locate  that in 3 ways:

* We can  use `./scriptname` if we are in the same directory
* We can use absolute path `/home/user1/sandbox/scriptname`
* putting our script in one of `$PATH` directories or editting `PATH` variable.

```
user1@ubuntu16-1:~/sandbox$ ./script1 
Hello World

user1@ubuntu16-1:~/sandbox$ /home/user1/sandbox/script1
Hello World

user1@ubuntu16-1:~$ echo $PATH
/home/user1/bin:/home/user1/.local/bin:/usr/local/bin:/usr/bin:/bin:/usr/local/games:/usr/games:/snap/bin

user1@ubuntu16-1:~$ mkdir bin
user1@ubuntu16-1:~$ cp sandbox/script1 bin/
user1@ubuntu16-1:~$ scr
screendump    script        script1       scriptreplay  
user1@ubuntu16-1:~$ script1
Hello World
```

### conditioning

In order to write useful programs, we almost always need the ability to check conditions and change the behavior of the program accordingly. Conditional statements give us this ability. 

The simplest form is the **if** statement, which has the genaral form:

### if

```
if [expression]
then
   command1 
   command2
else
   command3
   command4
fi
```

> the esle part is optional and can be ommited.

```
#!/bin/bash

MYOS="linux"
echo "Lets see What is your favirite OS"

if [ $MYOS = "linux" ] 
then
 echo "Haha happy to hear that"
else
 echo "I wish you liked linux"
fi
```

> The actual checking of the condition is done by `test` command which is writter as `test EXPRESSION` it is the same as `[ EXPRESSION ].`

Expressions take the following forms:

| Expression            | Meaning                                       |
| --------------------- | --------------------------------------------- |
| STRING1 **=** STRING2 | the strings are equal                         |
| STRING1 != STRING2    | the strings are not equal                     |
| INTEGER1 -eq INTEGER2 | INTEGER1 is equal to INTEGER2                 |
| INTEGER1 -ge INTEGER2 | INTEGER1 is greater than or equal to INTEGER2 |
| INTEGER1 -gt INTEGER2 | INTEGER1 is greater than INTEGER2             |
| INTEGER1 -le INTEGER2 | INTEGER1 is less than or equal to INTEGER2    |
| INTEGER1 -lt INTEGER2 | INTEGER1 is less than INTEGER2                |
| INTEGER1 -ne INTEGER2 | INTEGER1 is not equal to INTEGER2             |
| -n STRING             | the length of STRING is nonzero               |
| -z STRING             | the length of STRING is zero                  |
| -f FILE               | FILE exists and is a regular file             |
| -s FILE               | FILE exists and has a size greater than zero  |
| -X FILE               | FILE exists and execute (or s                 |
| -d FILE               | FILE exists and is a directory                |

### read

`read` command read a line from the standard input.

```
#!/bin/bash

MYOS="linux"
echo "Lets see What is your favirite OS"

if [ $MYOS = "linux" ] 
then
 echo "Haha happy to hear that"
else
 echo "I wish you liked linux"
fi
```

### loops

The purpose of loops is to repeat the same, or similar code a number of times. This number of times could be specified to a certain number, or the number of times could be dictated by a certain condition being met. There are usually a number of different types of loops  including for loops, while loops and‌ ... .

### for

The `for` loop processes each item in a sequence

```
for VAR in LIST;
do
  command1
  command2
done
```

> please py attention to syntax  `;` , `do` , `done` .

> LIST can be a list of strings, an array or output of commands, etc

```
#!/bin/bash

for i in 1 2 3 ;
do
 echo $i
done
```

### **seq**

**seq is a very usefull command. **it generates squence of numbers for loop from FIRST to LAST in steps of INCREMENT. 

```
seq [OPTION]... LAST
 ### or
seq [OPTION]... FIRST LAST
 ### or
seq [OPTION]... FIRST INCREMENT LAST
```

```
user1@ubuntu16-1:~/sandbox$ seq 3
1
2
3
user1@ubuntu16-1:~/sandbox$ seq 1 3
1
2
3
user1@ubuntu16-1:~/sandbox$ seq 1 10 2
1
user1@ubuntu16-1:~/sandbox$ seq 1 3 10
1
4
7
10
```

seq in for loop :

```
user1@ubuntu16-1:~/sandbox$ for i in $(seq 1 3); do echo $i; done
1
2
3
```

> We can also define a range with {START..END..INCREMENT} syntax and it would do the same thing.

for loop works with strings too:

```
#!/bin/bash

for MYFILE in $(ls);
do
    echo $MYFILE
done
```

### while

while loops evaluate a condition each time the loop starts and execute the command list if the condition is true. If the condition is not initially true, the commands are never executed.

```
while [condition]
do
    command1
    command2
done
```

example:

```
#!/bin/bash

MYVAR=3

while [ $MYVAR -gt 0 ]
do
  echo $MYVAR
  let MYVAR=MYVAR-1
done
```

> The **let** is a builtin command and  used to evaluate arithmetic expressions on shell variables. What does it mean ? Without let MYVAR=MYVAR-1 whould have "3-1" value as string! 

> If you want to use a variable value in an arithmetic expression, you don’t need to use `$` before the variable name, although you can if you want.

{% hint style="danger" %}
**infinite loops**

if the condition of while loop is never met, it goes running for ever, that is called infinite loop and we have to use ctrl+c to break that! 

infinite loops are not that much bad ! The OS is an infinite loop which constantly checks for input and response accordingly.
{% endhint %}

{% hint style="info" %}
**until**\
until loops execute the command list and evaluate a condition each time the loop ends. If the condition is true, the loop is executed again. Even if the condition is not initially true, the commands execute at least once.
{% endhint %}

### exec <a href="mailing-notifications-to-root" id="mailing-notifications-to-root"></a>

 **exec** command in Linux is used to execute a command from the bash itself. This command does not create a new process it just replaces the bash with the command to be executed. If the exec command is successful, it does not return to the calling process. try `exec BLAH `and `exec ls` and compare results.

### Mailing notifications to root <a href="mailing-notifications-to-root" id="mailing-notifications-to-root"></a>

For sending mail first we need to have `mailutils` installed. Then we can send mail:

```
root@ubuntu16-1:~# mail root@localhost
Cc: 
Subject: test mail!
this is my first email!
```

Suppose your script is running an administrative task on your system in the dead of night while you’re sound asleep. What happens when something goes wrong? Fortunately, it’s easy to mail error information or log files to yourself or to another administrator or to root. Simply pipe the message to the `mail` command, and use the `-s` option to add a subject line, 

```
user1@ubuntu16-1:~$ echo "Body!" | mail -s "Subject" root@localhost
```

> If you need to mail a log file, use the `<` redirection function to redirect it as input to the `mail` command. If you need to send several files, you can use `cat` to combine them and pipe the output to `mail`. 

{% hint style="info" %}
**bash scripting cheatsheet**

I also encourage you to visit my bash scripting cheatsheet at: [https://borosan.gitbook.io/bash-scripting/](https://borosan.gitbook.io/bash-scripting/)
{% endhint %}

.

.

.

[https://developer.ibm.com/tutorials/l-lpic1-105-2/](https://developer.ibm.com/tutorials/l-lpic1-105-2/)

[https://www.poftut.com/linux-bin-bash-shell-and-script-tutorial/](https://www.poftut.com/linux-bin-bash-shell-and-script-tutorial/)

[https://www.geeksforgeeks.org/declare-command-in-linux-with-examples/](https://www.geeksforgeeks.org/declare-command-in-linux-with-examples/)

[https://www.gnu.org/software/bash/manual/html_node/Command-Substitution.html](https://www.gnu.org/software/bash/manual/html_node/Command-Substitution.html)

[https://jadi.gitbooks.io/lpic1/content/1052\_customize_or_write_simple_scripts.html](https://jadi.gitbooks.io/lpic1/content/1052\_customize_or_write_simple_scripts.html)

[https://www.computerhope.com/unix/test.htm](https://www.computerhope.com/unix/test.htm)

[https://www.dpscomputing.com/blog/2012/09/13/programming-the-purpose-of-loops/](https://www.dpscomputing.com/blog/2012/09/13/programming-the-purpose-of-loops/)

[https://www.geeksforgeeks.org/seq-command-in-linux-with-examples/](https://www.geeksforgeeks.org/seq-command-in-linux-with-examples/)

[https://linux4one.com/bash-for-loop-with-examples/](https://linux4one.com/bash-for-loop-with-examples/)

[https://www.geeksforgeeks.org/let-command-in-linux-with-examples/](https://www.geeksforgeeks.org/let-command-in-linux-with-examples/)

[https://www.quora.com/What-are-some-practical-uses-of-infinite-loops](https://www.quora.com/What-are-some-practical-uses-of-infinite-loops)

[https://www.geeksforgeeks.org/exec-command-in-linux-with-examples/](https://www.geeksforgeeks.org/exec-command-in-linux-with-examples/)

.
