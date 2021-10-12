# 105.1. Customize and use the shell environment

## **105.1 Customize and use the shell environment**

**Weight**: 4

**Description:** Candidates should be able to customize shell environments to meet users’ needs. Candidates should be able to modify global and user profiles.

**Key Knowledge Areas:**

* Set environment variables (e.g. PATH) at login or when spawning a new shell
* Write Bash functions for frequently used sequences of commands
* Maintain skeleton directories for new user accounts
* Set command search path with the proper directory

**The following is a partial list of the used files, terms and utilities:**

* source
* /etc/bash.bashrc
* /etc/profile
* env
* export
* set
* unset
* \~/.bash_profile
* \~/.bash_login
* \~/.profile
* \~/.bashrc
* \~/.bash_logout
* function
* alias
* lists



We have already talk about environment variables in section "103-1". In this section we want to talk about user profiles and system wide profile , and how they interact when user log on.

### Login shell and Non login shell

The shell program, for example Bash, uses a collection of startup scripts to create an environment. Each script has a specific use and affects the login environment differently. Every subsequent script executed can override the values assigned by previous scripts.

Startup is configured differently for Login shells and Non login shells. 

1. **Login shells** : If you open a shell or terminal (or switch to one), and it asks you to log in (Username? Password?) before it gives you a prompt, it's a login shell.
2. **Non login shells **: If it doesn't ask you log in (like _gnome-terminal_), and lets you use it straight away, it's a non-login shell (GUI)

![](.gitbook/assets/custshell-loginshelvsnon.jpg)

#### Adding global configuration for login shell

### /etc/profile

/etc/profile contains Linux system wide environment and startup programs. It is used by all users with bash, ksh, sh shell. It only runs for login shell.  

### /etc/profile.d

if you need to customize the login environment for all users on your system,  you can use /etc/profile, but as  the distribution files in /etc, such as /etc/profile, can be modified by system updates, so it is better not to edit them directly. Rather, create additional files in /etc/profile.d.

#### adding global configs for non-login (interactive) shell

### /etc/bash.bashrc &  /etc/bashrc

You can use /etc/bash.bashrc ( or /etc/bashrc )\[based on your distro] for adding global configs and global  aliases.

#### user specfic configs

### /home/user/.bash_profile  &  /home/user/.bashrc

 `~/.bash_profile` and `~/.bashrc` are shell scripts that contain shell commands. These files are executed in a user's context when a new shell opens or when a user logs in so that their environment is set correctly. As we mentioned`~/.bash_profile` is executed for login shells and `~/.bashrc` is executed for interactive non-login shells.

 This means that when a user logs in (via username and password) to the console (either locally or remotely via something like SSH), the `~/.bash_profile` script is executed before the initial command prompt is returned to the user. After that, every time a new shell is opened, the `~/.bashrc` script is executed. 

> Most of the time PATH and env vars go into the in \~/.bash_profile and aliases go into the \~/.bashrc.

### Aliases

In bash, you can define aliases for commands. You use aliases to provide an alternative name for a command, to provide default parameters for the command, or sometimes to construct a new or more-complex command.

 You set aliases or list aliases with the `alias` command and remove them with the `unalias` command.

```
### list aliases
root@ubuntu16-1:~# alias 
alias egrep='egrep --color=auto'
alias fgrep='fgrep --color=auto'
alias grep='grep --color=auto'
alias l='ls -CF'
alias la='ls -A'
alias ll='ls -alF'
alias ls='ls --color=auto'

### unalias with unalias command
root@ubuntu16-1:~# unalias ll
root@ubuntu16-1:~# ll
ll: command not found

### setting an alias
root@ubuntu16-1:~# alias ll='ls -alF'
root@ubuntu16-1:~# ll
total 472
...
```

in order to make aliases permanent for user they are defined in `/home/user/.bashrc` :

```
...
# some more ls aliases
alias ll='ls -alF'
alias la='ls -A'
alias l='ls -CF'
...
```

>  Another common use of aliases is for the root user. The `cp`, `rm`, and `mv` commands are usually aliased to include the `-i` parameter, to help prevent accidental destruction of files.

### /etc/skel

You might be wondering how files like \~/.bash_profile, \~/.bashrc, or \~/.bash_logout got created in your home directory. These are skeleton files that are copied from /etc/skel.

```
root@ubuntu16-1:~# ls -1a /etc/skel/
.
..
.bash_logout
.bashrc
examples.desktop
.profile
```

### .bash_logout

The file .bash_logout is read and executed every time a login shell exits. It clears the screen whenever you log out. Without .bash_logout whatever you were working on could be visible for the next user!

```
root@ubuntu16-1:~# cat /etc/skel/.bash_logout 
# ~/.bash_logout: executed by bash(1) when login shell exits.

# when leaving the console clear the screen to increase privacy

if [ "$SHLVL" = 1 ]; then
    [ -x /usr/bin/clear_console ] && /usr/bin/clear_console -q
fi
```

### . and source

 If a script runs in a child shell, any variables it might export are lost when it returns to the parent.

```
root@ubuntu16-1:~# cat .profile 
# ~/.profile: executed by Bourne-compatible login shells.

if [ "$BASH" ]; then
  if [ -f ~/.bashrc ]; then
    . ~/.bashrc
  fi
fi

mesg n || true
```

 So if .profile runs the \~/.bashrc script, why aren’t the variables and functions that are exported from \~/.bashrc lost? The answer is that you run the script in the current environment by using the `source` (or `.`) command. 

### Shell functions

Aliases are useful, but what happens if you want to handle parameters? Aliases expand only the first word, and everything else on the command line is appended to the expansion. If you want to run a command with some parameters and then process the output somehow, you are out of luck with an alias. In this case, you need a shell function.

```
myfunc() {
    echo "hello $1"
}

# Same as above (alternate syntax)
function myfunc() {
    echo "hello $1"
}
```

`$1` returns the first argument

```
root@ubuntu16-1:~# myfunc "payam"
hello payam
```

Shell functions have a couple of advantages over aliases:

* You can handle parameters.
* You can use programming constructs, such as testing and looping, to enhance your processing.

{% hint style="info" %}
We can use `unset `command inorder to unset our defined function.
{% endhint %}

### lists

Shell supports a different type of variable called an array variable. This can hold multiple values at the same time. Arrays provide a method of grouping a set of variables. Instead of creating a new name for each variable that is required, you can use a single array variable that stores all the other variables.

```
array_name=(value1 ... valuen)

### example:
root@ubuntu16-1:~# mylist=(lets learn linux)

### for accessing value:  {array_name[index]}

### example:
root@ubuntu16-1:~# echo ${mylist[2]}
linux
```

Another example:

```
root@ubuntu16-1:~# mylist2=("we are" "learning" "lpic 1-102 exam")
root@ubuntu16-1:~# echo ${mylist2[2]}
lpic 1-101 exam
```

Keep your eyes on syntax!

.

.

.

[http://howtolamp.com/articles/difference-between-login-and-non-login-shell/](http://howtolamp.com/articles/difference-between-login-and-non-login-shell/)

[https://askubuntu.com/questions/155865/what-are-login-and-non-login-shells](https://askubuntu.com/questions/155865/what-are-login-and-non-login-shells)

[https://www.cyberciti.biz/faq/set-environment-variable-linux/](https://www.cyberciti.biz/faq/set-environment-variable-linux/)

[https://developer.ibm.com/tutorials/l-lpic1-105-1/](https://developer.ibm.com/tutorials/l-lpic1-105-1/)

[https://jadi.gitbooks.io/lpic1/content/1051\_customize_and_use_the_shell_environment.html](https://jadi.gitbooks.io/lpic1/content/1051\_customize_and_use_the_shell_environment.html)

[https://www.tutorialspoint.com/unix/unix-using-arrays.htm](https://www.tutorialspoint.com/unix/unix-using-arrays.htm)

With the special thanks of shawn powers.

.
