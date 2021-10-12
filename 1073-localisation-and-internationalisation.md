# 107.3. Localisation and internationalisation

**Weight:** 3

**Description:** Candidates should be able to localize a system in a different language than English. As well, an understanding of why LANG=C is useful when scripting.

**Key Knowledge Areas:**

* Configure locale settings and environment variables
* Configure timezone settings and environment variables

**Terms and Utilities:**

* /etc/timezone
* /etc/localtime
* /usr/share/zoneinfo/
* LC_\*
* LC_ALL
* LANG
* TZ
* /usr/bin/locale
* tzselect
* timedatectl
* date
* iconv
* UTF-8
* ISO-8859
* ASCII
* Unicode

There are thousands of different languages used throughout the world. Numbers and dates can be formatted differently, and there are over 40 alphabets in existence. People use either a 12-hour clock or a 24-hour clock for time. There are also different systems of measurement. In this lesson we will learn   how to configure our Linux system to adapt it to our  _locale_ .

## timezone

The term time zone can be used to describe several different things, but mostly it refers to the local time of a region or a country.The local time within a time zone is defined by its difference from Coordinated Universal Time (UTC), the world's time standard.

 There are a number of time management utilities available on Linux such as **date** and **timedatectl** commands to get the current timezone of system.

### date

 **date **command is used to display the system date and time. date command is also used to set date and time of the system. By default the date command displays the date in the time zone on which unix/linux operating system is configured.You must be the super-user (root) to change the date and time.(ubuntu16)

```
user1@ubuntu16-1:~$ date
Sun Feb 16 03:27:44 PST 2020
```

`date -u` Displays the time in GMT(Greenwich Mean Time)/UTC(Coordinated Universal Time )time zone.

```
user1@ubuntu16-1:~$ date -u
Sun Feb 16 11:30:09 UTC 2020
```

**Date forma**t : FORMAT is a sequence of characters which specifies how output will appear. The syntax is   The syntax is `date +% <format-options>` :

| date  format option | Purpose of Option                |
| ------------------- | -------------------------------- |
| %Y                  | year                             |
| %y                  | last two digits of year (00..99) |
| %m                  | month (01..12)                   |
| %d                  | day of month (e.g., 01)          |
| %D                  | %D date; same as %m/%d/%y        |
| %H                  | hour (00..23)                    |
| %M                  | minute (00..59)                  |

```
user1@ubuntu16-1:~$ date +"%Y%m%d-%H:%M"
20200216-03:49
```

### timedatectl

For all Linux distributions that use systemd. There should be a timedatectl command.

The timedatectl command allows us to query and change the configuration of the system clock and its settings, we can use this command to set or change the current date, time and timezone or enable automatic system clock synchronization with a remote NTP server (Next lesson).

```
user1@ubuntu16-1:~$ timedatectl
      Local time: Sun 2020-02-16 03:57:22 PST
  Universal time: Sun 2020-02-16 11:57:22 UTC
        RTC time: Sun 2020-02-16 11:57:22
       Time zone: America/Los_Angeles (PST, -0800)
 Network time on: yes
NTP synchronized: yes
 RTC in local TZ: no
```

#### Configuring user time zone

We can configure our time zone during OS installation process, using GUI, or we can use date and time setting in the GUI panel But as always there are some terminal tools which help us. In old days tzconfig command were used but it has been deprecated, instead use tzselect:

### tzselect 

 The **tzselect** program asks the user for information about the current location, and outputs the resulting timezone description to standard output. The output is suitable as a value for the **TZ** environment variable.

```
user1@ubuntu16-1:~$ tzselect 
Please identify a location so that time zone rules can be set correctly.
Please select a continent, ocean, "coord", or "TZ".
 1) Africa
 2) Americas
 3) Antarctica
 4) Asia
 5) Atlantic Ocean
 6) Australia
 7) Europe
 8) Indian Ocean
 9) Pacific Ocean
10) coord - I want to use geographical coordinates.
11) TZ - I want to specify the time zone using the Posix TZ format.
```

At the end the process suggest us to set TZ (Time Zone) Environment variable:

```
You can make this change permanent for yourself by appending the line
	TZ='Asia/Tehran'; export TZ
to the file '.profile' in your home directory; then log out and log in again.
```

 As noted in the output, you can set and export it in your .profile file if you want to use a time zone that is different from your system time zone.

#### configuring system time zone

### /usr/share/zoneinfo

The /usr/share/zoneinfo is a directory which keeps all the timezone info.

```
root@ubuntu16-1:~# ls -l /usr/share/zoneinfo/ | head
total 328
drwxr-xr-x  2 root root  4096 Nov  4  2018 Africa
drwxr-xr-x  6 root root 20480 Nov  4  2018 America
drwxr-xr-x  2 root root  4096 Nov  4  2018 Antarctica
drwxr-xr-x  2 root root  4096 Nov  4  2018 Arctic
drwxr-xr-x  2 root root 12288 Nov  4  2018 Asia
drwxr-xr-x  2 root root  4096 Nov  4  2018 Atlantic
drwxr-xr-x  2 root root  4096 Nov  4  2018 Australia
drwxr-xr-x  2 root root  4096 Nov  4  2018 Brazil
drwxr-xr-x  2 root root  4096 Nov  4  2018 Canada
...
```

and it contains required time zone binary files:

```
root@ubuntu16-1:~# cat /usr/share/zoneinfo/Asia/Tehran 
TZif2e��l}������Ht-@�@0�:@Ug�EJ�7��-�( v�(۝�)˜
�*�"�+��H,�V8-��.���/o7H0a�81Pj�2B��32��4%u�5#H
6�86�V�7�ܸ8֊H9�8 . . .
```

### /etc/localtime

Linux looks at /etc/localtime to determine the current time of your machine. This can either be a symbolic link to the correct time zone or a direct copy of the time zone file.

```
root@ubuntu16-1:~# ls -l /etc/localtime 
lrwxrwxrwx 1 root root 39 Nov  4  2018 /etc/localtime -> /usr/share/zoneinfo/America/Los_Angeles
```

we can use one of bellow commands to change or system time zone(Tehran):

* `ln -s /usr/share/zoneinfo/Asia/Tehran /etc/localtime`
* `cp /usr/share/zoneinfo/Asia/Tehran /etc/localtime`

> if you got an error while trying to create symlink, remove it first: `sudo  unlink /etc/localtime` or ` sudo rm -rf /etc/localtime`

### /etc/timezone

This file is holding timezone name on debian based systems. `/etc/sysconfig/clock ` is holding timezone name on RHEL based systems.

{% hint style="info" %}
There are some other  ways to configure the time zone on Linux distributions.

* using **timedatectl** in distributions with systemd:`timedatectl set-timezone Europe/Amsterdam`
* Using dpkg-reconfigure  in (Debian/Ubuntu) distributions: `dpkg-reconfigure tzdata`
{% endhint %}

## Configuring Languages

We can configure system languages from settings (Regional\&Languages) but there is always terminal tools

### locale

 A **locale** is a set of environmental variables that defines the language, country, and character encoding settings (or any other special variant preferences) for your applications and shell session on a Linux system. These environmental variables are used by system libraries and locale-aware applications on the system.

>  To view information about the current installed locale, use the **locale** :

```
root@ubuntu16-1:~# locale
LANG=en_US.UTF-8
LANGUAGE=
LC_CTYPE="en_US.UTF-8"
LC_NUMERIC="en_US.UTF-8"
LC_TIME="en_US.UTF-8"
LC_COLLATE="en_US.UTF-8"
LC_MONETARY="en_US.UTF-8"
LC_MESSAGES="en_US.UTF-8"
LC_PAPER="en_US.UTF-8"
LC_NAME="en_US.UTF-8"
LC_ADDRESS="en_US.UTF-8"
LC_TELEPHONE="en_US.UTF-8"
LC_MEASUREMENT="en_US.UTF-8"
LC_IDENTIFICATION="en_US.UTF-8"
LC_ALL=
```

> variables format is like: "Language_COUNTRY._ENCODING"_

### LANG

 The **LANG** environment variable value is established at installation. (This Provides default value for LC_\* variables unless that  variable is set). 

* LANGUAGE
* LC_CTYPE How characters are classified as letters, numbers etc. This determines things like how characters are converted between upper and lower case. 
* LC_NUMERIC How you format your numbers. For example, in many countries a period (.) is used as a decimal separator, while others use a comma (,).
* LC_TIME How your time and date are formatted. Use for example "en_DK.UTF-8" to get a 24-hour-clock in some programs. 
* LC_COLLATE How strings (file names...) are alphabetically sorted. Using the "C" or "POSIX" locale here results in a strcmp()-like sort order, which may be preferable to language-specific locales. 
* LC_MONETARY What currency you use, its name, and its symbol. 
* LC_MESSAGES What language should be used for system messages. 
* LC_PAPER Paper sizes: 11 x 17 inches, A4, etc. 
* LC_NAME How names are represented (surname first or last, etc.). 
* LC_ADDRESS How addresses are formatted (country first or last, where zip code goes etc.). 
* LC_TELEPHONE What your telephone numbers look like.
* LC_MEASUREMENT What units of measurement are used (feet, meters, pounds, kilos etc.).
* LC_IDENTIFICATION Metadata about the locale information.

as an example, lets change LC_TIME to another thing:

```
root@ubuntu16-1:~# LC_TIME=en_GB.UTF-8 date
Sun 16 Feb 19:04:37 +0330 2020
root@ubuntu16-1:~# LC_TIME=en_US.UTF-8 date
Sun Feb 16 19:04:44 +0330 2020
```

### LC_ALL

 Overrides the value of the **LANG** environment variable and the values of any other **LC_\*** environment variables.

```
root@ubuntu16-1:~# export LC_ALL="en_GB.UTF-8"
root@ubuntu16-1:~# locale
LANG=en_US.UTF-8
LANGUAGE=
LC_CTYPE="en_GB.UTF-8"
LC_NUMERIC="en_GB.UTF-8"
LC_TIME="en_GB.UTF-8"
LC_COLLATE="en_GB.UTF-8"
LC_MONETARY="en_GB.UTF-8"
LC_MESSAGES="en_GB.UTF-8"
LC_PAPER="en_GB.UTF-8"
LC_NAME="en_GB.UTF-8"
LC_ADDRESS="en_GB.UTF-8"
LC_TELEPHONE="en_GB.UTF-8"
LC_MEASUREMENT="en_GB.UTF-8"
LC_IDENTIFICATION="en_GB.UTF-8"
LC_ALL=en_GB.UTF-8
root@ubuntu16-1:~# unset LC_ALL
```

> we can also use bellow command to work with locale ans set or install languages :
>
> * `dpkg-reconfigure locales `(Debian) 
> * `system-config-language` (Redhat)
>
> there is also  _localectl _in systemd systems, which display and control system locale settings.

### LANG=C

The LANG=C does two things:

* It forces applications to use the default language for output:

```
$ LC_ALL=es_ES man
¿Qué página de manual desea?

$ LC_ALL=C man
What manual page do you want?
```

* forces sorting to be byte-wise

```
$ LC_ALL=en_US sort <<< $'a\nb\nA\nB'
a
A
b
B

$ LC_ALL=C sort <<< $'a\nb\nA\nB'
A
B
a
b
```

It is also possible to do a LC_ALL=C.

### Character Encoding

A computer represents information in numbers and, when they need to be communicated to Humans (and vice versa) they need to be encoded.

* **ASCII **is a seven-bit encoding technique which assigns a number to each of the 128 characters used most frequently in American English. This allows most computers to record and display basic text. ASCII does not include symbols frequently used in other countries, such as the British pound symbol or the German umlaut. ASCII is understood by almost all email and communications software.
* **ISO 8859** is an eight-bit extension to ASCII developed by ISO (the International Organization for Standardization). ISO 8859 includes the 128 ASCII characters along with an additional 128 characters, such as the British pound symbol and the American cent symbol. Several variations of the ISO 8859 standard exist for different language families
* **Unicode** is an attempt by ISO and the Unicode Consortium to develop a coding system for electronic text that includes every written alphabet in existence. Unicode uses 8-, 16-, or 32-bit characters depending on the specific representation, so Unicode documents often require up to twice as much disk space as ASCII



* ASCII: 7 bits. 128 code points.
* ISO-8859-1: 8 bits. 256 code points.
* UTF-8: 8-32 bits (1-4 bytes). 1,112,064 code points.

> Check for available encoding on your system with `locale -m` command.

> use `file `command to see character encoding of a file.

### iconv

We can  use the `iconv` program to convert between character encodings. Obviously, if you go from a large character set to a smaller one, the conversion does not happen properly.

```
iconv [options] [-f from-encoding] [-t to-encoding] [inputfile]...
```

example:

```
iconv -f ASCII -t UTF-8 /path/to/MyOldFile > MyNewFile
```

If no input file is provided then it reads from standard input. Similarly, if no output file is given then it writes to standard output. Example:

> `iconv -l` list all known character set encodings..

.

.

[https://developer.ibm.com/technologies/linux/tutorials/l-lpic1-map/](https://developer.ibm.com/technologies/linux/tutorials/l-lpic1-map/)

[https://www.tecmint.com/check-linux-timezone/](https://www.tecmint.com/check-linux-timezone/)

[https://www.geeksforgeeks.org/date-command-linux-examples/](https://www.geeksforgeeks.org/date-command-linux-examples/)

[https://www.computerhope.com/unix/udate.htm](https://www.computerhope.com/unix/udate.htm)

[https://www.tecmint.com/set-time-timezone-and-synchronize-time-using-timedatectl-command/](https://www.tecmint.com/set-time-timezone-and-synchronize-time-using-timedatectl-command/)

[https://www.timeanddate.com/time/time-zones.html](https://www.timeanddate.com/time/time-zones.html)

[https://jadi.gitbooks.io/lpic1/content/1073\_localisation_and_internationalisation.html](https://jadi.gitbooks.io/lpic1/content/1073\_localisation_and_internationalisation.html)

[https://linux.die.net/man/8/tzselect](https://linux.die.net/man/8/tzselect)

[https://linuxacademy.com/blog/linux/changing-the-time-zone-in-linux-command-line/](https://linuxacademy.com/blog/linux/changing-the-time-zone-in-linux-command-line/)

[https://www.2daygeek.com/linux-change-check-timezone/](https://www.2daygeek.com/linux-change-check-timezone/)

[https://linux-audit.com/configure-the-time-zone-tz-on-linux-systems/](https://linux-audit.com/configure-the-time-zone-tz-on-linux-systems/)

[https://help.ubuntu.com/community/Locale](https://help.ubuntu.com/community/Locale)

[https://docs.oracle.com/cd/E23824\_01/html/E26033/glmbx.html](https://docs.oracle.com/cd/E23824\_01/html/E26033/glmbx.html)

[https://unix.stackexchange.com/questions/87745/what-does-lc-all-c-do](https://unix.stackexchange.com/questions/87745/what-does-lc-all-c-do)

[https://www.praim.com/en/news/character-encodings-in-linux-ascii-utf-8-and-iso-8859/](https://www.praim.com/en/news/character-encodings-in-linux-ascii-utf-8-and-iso-8859/)

[https://kb.iu.edu/d/ahfr](https://kb.iu.edu/d/ahfr)

[https://www.geeksforgeeks.org/iconv-command-in-linux-with-examples/](https://www.geeksforgeeks.org/iconv-command-in-linux-with-examples/)

.
