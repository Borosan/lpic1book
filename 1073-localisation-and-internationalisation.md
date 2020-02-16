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
* LC\_\*
* LC\_ALL
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

The term time zone can be used to describe several different things, but mostly it refers to the local time of a region or a country.The local time within a time zone is defined by its difference from Coordinated Universal Time \(UTC\), the world's time standard.

 There are a number of time management utilities available on Linux such as **date** and **timedatectl** commands to get the current timezone of system.

### date

 **date** command is used to display the system date and time. date command is also used to set date and time of the system. By default the date command displays the date in the time zone on which unix/linux operating system is configured.You must be the super-user \(root\) to change the date and time.\(ubuntu16\)

```text
user1@ubuntu16-1:~$ date
Sun Feb 16 03:27:44 PST 2020
```

`date -u` Displays the time in GMT\(Greenwich Mean Time\)/UTC\(Coordinated Universal Time \)time zone.

```text
user1@ubuntu16-1:~$ date -u
Sun Feb 16 11:30:09 UTC 2020
```

**Date forma**t : FORMAT is a sequence of characters which specifies how output will appear. The syntax is   The syntax is `date +% <format-options>` :

| date  format option | Purpose of Option |
| :--- | :--- |
| %Y | year |
| %y | last two digits of year \(00..99\) |
| %m | month \(01..12\) |
| %d | day of month \(e.g., 01\) |
| %D | %D date; same as %m/%d/%y |
| %H | hour \(00..23\) |
| %M | minute \(00..59\) |

```text
user1@ubuntu16-1:~$ date +"%Y%m%d-%H:%M"
20200216-03:49
```

### timedatectl

The timedatectl command allows us to query and change the configuration of the system clock and its settings, we can use this command to set or change the current date, time and timezone or enable automatic system clock synchronization with a remote NTP server \(Next lesson\).

```text
user1@ubuntu16-1:~$ timedatectl
      Local time: Sun 2020-02-16 03:57:22 PST
  Universal time: Sun 2020-02-16 11:57:22 UTC
        RTC time: Sun 2020-02-16 11:57:22
       Time zone: America/Los_Angeles (PST, -0800)
 Network time on: yes
NTP synchronized: yes
 RTC in local TZ: no
```

#### Configuring time zone

We can configure our time zone during OS installation process, using GUI, or we can use date and time setting in the panel/ But as always there are some terminal tools which help us. In old days tzconfig command were used but it has been deprecated, instead use tzselect:

### tzselect 

 The **tzselect** program asks the user for information about the current location, and outputs the resulting timezone description to standard output. The output is suitable as a value for the **TZ** environment variable.

```text
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

At the end the process suggest us to set TZ Environment variable



Note the reference to the TZ environment variable. You might find that if you attempt to display the TZ environment variable, it is not set. As noted in the output, you can set and export it in your .profile file if you want to use a time zone that is different from your system time zone.

.

.

.

[https://developer.ibm.com/technologies/linux/tutorials/l-lpic1-map/](https://developer.ibm.com/technologies/linux/tutorials/l-lpic1-map/)

[https://www.tecmint.com/check-linux-timezone/](https://www.tecmint.com/check-linux-timezone/)

[https://www.geeksforgeeks.org/date-command-linux-examples/](https://www.geeksforgeeks.org/date-command-linux-examples/)

[https://www.computerhope.com/unix/udate.htm](https://www.computerhope.com/unix/udate.htm)

[https://www.tecmint.com/set-time-timezone-and-synchronize-time-using-timedatectl-command/](https://www.tecmint.com/set-time-timezone-and-synchronize-time-using-timedatectl-command/)

[https://www.timeanddate.com/time/time-zones.html](https://www.timeanddate.com/time/time-zones.html)

[https://jadi.gitbooks.io/lpic1/content/1073\_localisation\_and\_internationalisation.html](https://jadi.gitbooks.io/lpic1/content/1073_localisation_and_internationalisation.html)

.

