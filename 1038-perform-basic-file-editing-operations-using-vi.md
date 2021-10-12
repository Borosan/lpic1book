# 103.8. Perform basic file editing operations using vi

**Weight:** 3

**Description:** Candidates should be able to edit text files using vi. This objective includes vi navigation, basic vi modes, inserting, editing, deleting, copying and finding text.

**Key Knowledge Areas:**

* Navigate a document using vi
* Use basic vi modes
* Insert, edit, delete, copy and find text

**Terms and Utilities:**

* vi
* /, ?
* h,j,k,l
* i, o, a
* c, d, p, y, dd, yy
* ZZ, :w!, :q!, :e!

The need to learn how to use text editors in Linux is indisputable. Every system administrator and engineer deal with configuration (plain text) files on a daily basis, and most times this is done purely using one or more tools from a command-line interface (such as **nano**, **vi**, or **emacs**).

### vi

The vi editor (visual editor) is almost certainly on every Linux and UNIX system. In fact, if a system has just one editor, it’s probably vi, so it’s worth knowing your way around in vi.

Using vi editor, we can edit an existing file or create a new file from scratch. we can also use this editor to just read a text file.

> vi editor is great even trough ssh sessions!

#### vi or vim ?

 Most Linux distributions now ship with the vim (for **V**i **IM**proved) editor rather than classic vi. Vim is upward compatible with vi and has a graphical mode available (gvim) as well as the standard vi text mode interface. The `vi` command is usually an alias or symbolic link to the vim program.

 There are several versions of vim: tiny, small, normal, big, and huge. We can find out what version of vim we are running and what features are included by using the command(ubuntu 16.04 here ):

```
root@ubuntu16-1:~# vi --version
VIM - Vi IMproved 7.4 (2013 Aug 10, compiled Nov 24 2016 16:44:48)
Included patches: 1-1689
Extra patches: 8.0.0056
Modified by pkg-vim-maintainers@lists.alioth.debian.org
Compiled by pkg-vim-maintainers@lists.alioth.debian.org
Huge version without GUI.  Features included (+) or not (-):
+acl             +farsi           +mouse_netterm   +tag_binary
+arabic          +file_in_path    +mouse_sgr       +tag_old_static
+autocmd         +find_in_path    -mouse_sysmouse  -tag_any_white
-balloon_eval    +float           +mouse_urxvt     -tcl
-browse          +folding         +mouse_xterm     +terminfo
++builtin_terms  -footer          +multi_byte      +termresponse
+byte_offset     +fork()          +multi_lang      +textobjects
+channel         +gettext         -mzscheme        +timers
...
```

Inorder to read, create or modify a file with vi, give the file name to it:

```
vi filename
```

{% hint style="info" %}
**if you just type vi(m) and hit enter, the vi(m) version will be displayed:**

```
                              VIM - Vi IMproved                                
~                                                                               
~                               version 7.4.1689                                
~                           by Bram Moolenaar et al.                            
~           Modified by pkg-vim-maintainers@lists.alioth.debian.org             
~                 Vim is open source and freely distributable                   
~                                                                               
~                        Help poor children in Uganda!                          
~                type  :help iccf<Enter>       for information                  
~                                                                               
~                type  :q<Enter>               to exit                          
~                type  :help<Enter>  or  <F1>  for on-line help                 
~                type  :help version7<Enter>   for version info        
```
{% endhint %}

Okey lets start learning vi(m):

### vi modes

Vim actually has three modes: **insert mode**, **command mode**, and **escape** ** (last-line) mode**. Let’s start with the default mode you’ll see when you start up Vim–command mode.

* **command mode** :  When you run vim filename to edit a file, Vim starts out in command mode. This means that all the alphanumeric keys are bound to commands, rather than inserting those characters.(save, quit, search/replace, navigate around, execute macros,...)
* **insert mode** :  _To enter the insert mode, type i (for “insert”)_ and now the keys will behave as you’d expect. You can type normally until you want to make a correction, save the file, or perform another operation that’s reserved for command mode or escape (last-line) mode. _To get out of insert mode, hit the Escape key._
* **escape (last-line) mode** :  Once you press Escape, you’re in command mode again. What if you’d like to save your file or search through your document? No problem, _press : and Vim will switch to escape (last-line) mode_. Vim is now waiting for you to enter a command like :w to write the file or :q to exit the editor.

![](.gitbook/assets/performbasicfileop-vimodes.jpg)

If that all sounds complicated, it’s really not. It does take a few days to start training your brain to move between the modes and memorizing the most important keys for movement, commands, and so on.

####  Moving the cursor 

The first thing you’ll want to learn is how to move around a file. When you’re in command mode, you’ll want to remember the following keys and what they do:

| Key    | function                                                       |
| ------ | -------------------------------------------------------------- |
| h      | Move left one character on the current line                    |
| j      | Move down to the next line                                     |
| k      | Move up to the previous line                                   |
| l      | Move right one character on the current line                   |
| w      | Move to the next word on the current line                      |
| e      | Move to the next end of word on the current line               |
| b      | Move to the previous beginning of the word on the current line |
| Crtl-f | Scroll forward one page                                        |
| Ctrl-b |  Scroll backward one page                                      |

>  If you type a number before any of these commands, then the command will be executed that many times. This number is called a _repetition count_ or simply _count_. For example, 5h will move left five characters. You can use repetition counts with many vi commands.

#### Jumping  <a href="moving-to-lines" id="moving-to-lines"></a>

| key | function                 |
| --- | ------------------------ |
| H   | move to top of screen    |
| M   | move to middle of screen |
| L   | move to bottom of screen |

| key | function                                                              |
| --- | --------------------------------------------------------------------- |
| W   | jump forwards to the start of a word (words can contain punctuation)  |
| E   | jump forwards to the end of a word (words can contain punctuation)    |
| B   | jump backwards to the start of a word (words can contain punctuation) |

| key | function                                          |
| --- | ------------------------------------------------- |
| 0   | jump backwards to the start of line               |
| ^   | jump to the first non-blank character of the line |
| $   | jump to the end of the line                       |
| g\_ | jump to the last non-blank character of the line  |
| gg  | go to the first line of the document              |
| G   | go to the last line of the document               |

### Editing text <a href="editing-text" id="editing-text"></a>

Now that you can open a file in vi, move around it and get out, it’s time to learn how to edit the text in the file

| key( for inserting) | function                                                       |
| ------------------- | -------------------------------------------------------------- |
| i                   | insert text before cursor, until  hit                          |
| I                   | insert text at beginning of current line, until  hit           |
| a                   | append text after cursor, until  hit                           |
| A                   | append text to end of current line, until  hit                 |
| o                   | open and put text in a new line below current line, until  hit |
| O                   | open and put text in a new line above current line, until  hit |

| key(for changing) | function                                                                                    |
| ----------------- | ------------------------------------------------------------------------------------------- |
| r                 | replace single character under cursor (no  needed)                                          |
| R                 | replace characters, starting with current cursor position, until  hit                       |
| cw                | change the current word with new text, starting with the character under cursor, until  hit |
| cNw               | change N words beginning with character under cursor, until  hit; e.g., c5w changes 5 words |
| C                 | change (replace) the characters in the current line, until  hit                             |
| cc                | change (replace) the entire current line, stopping when  is hit                             |
| Ncc or cNc        | change (replace) the next N lines, starting with the current line, stopping when  is hit    |

| key (for deleting) | function                                                                        |
| ------------------ | ------------------------------------------------------------------------------- |
| x                  | delete single character under cursor                                            |
| Nx                 | delete N characters, starting with character under cursor                       |
| dw                 | delete the single word beginning with character under cursor                    |
| dNw                | delete N words beginning with character under cursor; e.g., d5w deletes 5 words |
| d^                 | delete  start of line till the cursor                                           |
| d$ and D           | delete the remainder of the line, starting with current cursor position         |
| dd                 | delete entire current line                                                      |
| Ndd or dNd         | delete N lines, beginning with the current line; e.g., 5dd deletes 5 lines      |

| key(for cutting and pasting)  | function                                                                       |
| ----------------------------- | ------------------------------------------------------------------------------ |
| yy                            | copy (yank, cut) the current line into the buffer                              |
| Nyy or yNy                    | copy (yank, cut) the next N lines, including the current line, into the buffer |
| p                             | put (paste) the line(s) in the buffer into the text after the current line     |
| P                             | put (paste) the line(s) in the buffer into the text before the current line    |

### searching

| key      | function                                                       |
| -------- | -------------------------------------------------------------- |
| /string  | search forward for occurrence of string in text                |
| ? string | search backward for occurrence of string in text               |
| n        | move to next occurrence of search string                       |
| N        | move to next occurrence of search string in opposite direction |

### replacing

| key             | function                                                    |
| --------------- | ----------------------------------------------------------- |
| :s/old/new/     | replace first old with new in just that  line               |
| :s/old/new/g    | replace all old with new in just that  line                 |
| :%s/old/new/g   | replace all old with new throughout file                    |
| :%s/old/new/gc  | replace all old with new throughout file with confirmations |
| :%s/old/new/gic | same as above but case insensitive                          |
| :noh            | remove highlighting of search matches                       |

### Exiting

If you’re in insert mode, hit Escape. Then enter : and you’ll see a line at the bottom of the screen with a cursor ready to take input.

| key        | function                                                                     |
| ---------- | ---------------------------------------------------------------------------- |
| :w         |  That will write the file to the existing filename,but don't exit.           |
| :w  _file_ | If you don’t have a filename or want to write out to a different _file name_ |
| :q         | quit (fails if there are unsaved changes)                                    |
| :q!        | Quit editing without saving.                                                 |
| :wq        | write (save) and quit (throw and error if file is not writable)              |
| :wq!       | try to write and quit (if it is not writable, quit without saving! )         |
| ZZ and :x  | Exit and save the file if modified                                           |
| q! or ZQ   | quit and throw away unsaved changes                                          |

Press ESC to return back to the normal command mode!

> When you want to write and exit from a file consider  you permissions, the bellow command write out the current file using sudo:
>
> :w !sudo tee % 
>
> * :w – Write a file (actually buffer). 
> * !sudo – Call shell with sudo command. 
> * tee – The output of write (vim :w) command redirected using tee. 
> * % – The % is nothing but current file name.

### reloading

| key | function                                           |
| --- | -------------------------------------------------- |
| :e  |  (short for `:edit`) reload the file from the disk |
| :e! | it will discard local changes and reload.          |

{% hint style="success" %}
### VIM Tip : running external commands in a shell

It usuaslly happens that we need to run a command in shell to see the result of file which we are edditing. It could be happened in two ways:

1.We could suspend your session (ctrl-Z), and then run the command in your shell.That’s a lot of keystrokes, though !

2.So, instead, you could use vim’s built-in “run a shell command”:

> :!cmd Run a shell command, shows you the output and prompts you before returning to your current buffer.
{% endhint %}

### help

Need help? you can use `:help` or` :help keyword`  and vim will open help for keyword.

.

.

.

[https://developer.ibm.com/tutorials/l-lpic1-103-8/](https://developer.ibm.com/tutorials/l-lpic1-103-8/)

[https://www.tecmint.com/learn-vi-and-vim-editor-tips-and-tricks-in-linux/](https://www.tecmint.com/learn-vi-and-vim-editor-tips-and-tricks-in-linux/)

[https://www.geeksforgeeks.org/vi-editor-unix/](https://www.geeksforgeeks.org/vi-editor-unix/)

[https://www.linux.com/tutorials/vim-101-beginners-guide-vim/](https://www.linux.com/tutorials/vim-101-beginners-guide-vim/)

[https://vim.rtorr.com/](https://vim.rtorr.com)

[https://www.cs.colostate.edu/helpdocs/vi.html](https://www.cs.colostate.edu/helpdocs/vi.html)

[https://www.endpoint.com/blog/2009/03/10/vim-tip-of-day-running-external](https://www.endpoint.com/blog/2009/03/10/vim-tip-of-day-running-external)

.
